## External Integration

---

Technology products frequently integrate with 3rd party platforms e.g Maps, Slack etc. The following guidelines should be observed when integrating with a 3rd party.

- Each integration should be mapped to an appropriate parent entity within technology. For example Slack users are mapped to Users entities on the User microservice.
- Every record type on the 3rd party should have only one parent entity.
- All 3rd party IDs and associated information should be available as part of the parent entity information and exposed when necessary. The following example is a user payload with associated 3rd party data

```
{
  id: 1,
  name: "John Smith",
  email: "john.smith@andela.com",
  metadata: {
    facebook: {
      user: {
        id: "1111",
      }
    },
    salesforce: {
      contact: {
        id: "2222",
      }
    }
  }
}
```
