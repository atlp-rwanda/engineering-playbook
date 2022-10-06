# Table of Contents:

1. [Creating New gRPC Endpoints](#creating-new-grpc-endpoints)

***

## Creating New gRPC Endpoints

This walkthrough uses references from [micro-pulse-service](https://github.com/andela/micro-pulse-service).

Andela Systems architecture implements [CQRS](https://medium.com/technology-learning/event-sourcing-and-cqrs-a-look-at-kafka-e0c1b90d17d8#.ansu5rx8v). Based on this, the READ and WRITE operations use different models. We are separating the READ and WRITE concerns in that the READ operations whereby we get data directly  from the database is handled normally whereas for WRITE operations which involve changing the state of data, we employ use of pubsub as an eventstore.

**NOTE:** gRPC endpoints must be added from [micro-shared](https://github.com/andela/micro-shared).

### Read Operation endpoint

We'll use `list ratings` operation to map to an endpoint for demonstration purposes
  * In `micro-shared`, create a proto definition message type
    To do this navigate  to `micro-shared/pulse/pulse-svc.proto` file
    ```
    /*
      List ratings.

      This endpoint list all available ratings.
    */
    rpc ListRatings (pulse.ListRatingsRequest) returns (pulse.Ratings) {
      option (google.api.http) = {
        get: "/api/v1/pulse/ratings"
      };
    }
    ```

    `ListRatings` is the endpoint, `pulse.ListRatingsRequest` the passed arguments and `pulse.Ratings` is the returned message containing the `Ratings` details. Create the messages in `micro-shared/pulse/pulse.proto` file as follows:

    `ListRatingsRequest` message type shows rating to be queried.

    ```
    message ListRatingsRequest {
      int32 limit = 1;
      int32 page = 2;
    }
    ```

    `Ratings` message type shows details of the queried ratings.

    ```
    message Ratings {
      repeated Rating values = 1;
      int32 count = 2;
    }
    ```

* In `micro-pulse`, create a function in ratings controller that will return list of all ratings  
  - Go to `controllers/ratings.js`. In our case we have the `index` method.  
  - Navigate to `server/index.js` to map the `listRatings` endpoint to the `index` method.

### Writing tests for the method
We need to create tests for the `index` controller. For our tests to work we need to have seed data. Since we want to retrieve data from the database we have to populate it with that data. This is done in the `/db/seeds` folder.
Each time the test command  is run the database is cleared first, then populated with the data and finally the tests are run.

Navigate to `tests/component/ratings.js` to  define our tests.
Load service descriptors from pulse.proto file.

```
const pulseProto = grpc.load(
  { root: protoPath, file: 'pulse/pulse-svc.proto' },
  'proto',
  { convertFieldsToCamelCase: true }
);
```
Then create a gRPC client to be able to connect to the pulse service. The `SERVICE_URL` represents the server address and port. This is where the server will be listening for the client requests. It's value for the pulse service is `SERVICE_URL='0.0.0.0:50067`.

```
const Client = new pulseProto.pulse.PulseService(process.env.SERVICE_URL,
    grpc.credentials.createInsecure());
```

Finally create a test case and call listRatings which maps to the `index` controller.

```
it('lists all ratings with ALL data', (done) => {
  client.listRatings({}, readWriteMeta, (err, res) => {
    const data = res.values;
    expect(data.length).toBe(1);
    // ensure sensitive data ARE within each rating
    data.forEach((rating) => {
      expect(rating.actions.length >= 0).toBeTruthy();
      expect(rating.clientConcern.length).toBeGreaterThan(0);
      expect(rating.comment.length).toBeGreaterThan(0);
    });
    done();
  });
});
```


### Write Operation endpoint
We'll use `create rating` operation to map to an endpoint for demonstration purposes.  
* The first two steps of the Read operation apply with only minor modifications:

```
/*
  Create rating.

  This endpoint create a rating.
  */
  rpc CreateRating (pulse.RatingRequest) returns (pulse.Rating) {
    option (google.api.http) = {
      post: "/api/v1/pulse/ratings"
      body: "*"
    };
  }

``` 
  `CreateRating` is the endpoint, `pulse.RatingRequest` is the required parameter when creating a new rating and `pulse.Rating` is the returned rating details once created.
  
```
message RatingRequest {
  string user_id = 2;
  string user_name = 3;
  string user_role = 4;
  int32 score = 5;
  string comment = 6;
  string client_id = 8;
  string pulse = 10;
  string attendees = 11;
  string client_feelings = 12;
  string client_concern = 13;
  repeated Action actions = 14;
  string pushed_to_slack = 15;
  string pushed_to_salesforce = 16;
}


message Rating {
  string id = 1;
  string user_id = 2;
  string user_name = 3;
  string user_role = 4;
  int32 score = 5;
  string comment = 6; // General Notes
  string created_at = 7;
  string client_id = 8;
  string updated_at = 9;
  string pulse = 10;
  string attendees = 11;
  string client_feelings = 12;
  string client_concern = 13;
  repeated Action actions = 14;
  string pushed_to_slack = 15;
  string pushed_to_salesforce = 16;
}

```

In micro-pulse, create a function in ratings controllers that will create a rating

* Go to controllers/ratings.js. In our case we have the `create` method.
* Navigate to server/index.js to map the createRating endpoint to the `create` method.

  
### Writing tests for the methods
The procedure is the same as for Read operation for controller tests but we also have to write tests for event handlers for this Write operation.  
Navigate to `tests/component/ratings.js` to add controller test
```
it('creates a rating and action items', (done) => {
  const newAction = {
    task: 'Fellow responds slowly to Slack',
    owner: 'Lisbi Abraham',
    dueDate: mockAction.dueDate.toISOString(),
    status: 'unresolved',
    resolvedBy: 'Divya Gunasekaran',
  };
  const newRating = {
    userId: '5',
    userName: 'Tupac Shakur',
    userRole: 'Success',
    score: 1,
    comment: 'Take money',
    clientId: '1',
    pulse: 'Happy',
    attendees: 'Victor, Bukky',
    clientFeelings: 'Happy',
    clientConcern: 'None',
    actions: [newAction],
  };
  client.createRating(newRating, (err, res) => {
    expect(res.userId).toBe(newRating.userId);
    expect(res.actions[0].task).toBe(newAction.task);
    done();
  });
});
```

Then go to `tests/unit/events/ratings.js` to write the event handler test.
An event handler takes in a payload and a callback. In this case the payload is the mockRating. This is what is sent when create rating request is made. Other services depending on pulse can subscribe to this event by subscribing to `pulse`.

```
const mockRating = {
  id: '-KoDiD7TNwWkSAEAAaK0',
  userId: '56',
  userName: 'Japheth Obala',
  userRole: 'Success',
  score: 1,
  comment: 'Knows what he does',
  clientConcern: 'I am very concerned',
  clientId: '-Kd7w6Aq0j6qVihTMIcn',
  createdAt: new Date('2016-10-01T00:00:00.000Z'),
  updatedAt: new Date('2016-10-01T00:00:00.000Z'),
};

const anotherMockAction = {
  id: '79srtre',
  ratingId: '89hj',
  task: 'Fellow responds slowly to Slack',
  owner: 'Lisbi Abraham',
  dueDate: '2018-03-19T21:55:05.000Z',
  status: 'unresolved',
  resolvedBy: 'Divya Gunasekaran',
};
```

Finally write the tests case.

```
it('creates a rating successfully', (done) => {
  const newRating = Object.assign({}, mockRating, 
    { id: '-KoDrQcz4FfwN3U5KnGs', actions: [anotherMockAction] }
  );
  newRating.actions = 
    handlers.createRating(newRating, (err, res) => {
      expect(res.done).toBe(true);
      done();
    });
  });
```