### Table of Contents
***
1. [Unit Testing](#unit-testing)
2. [Acceptance Testing](#acceptance-testing)

### Unit Testing
***
Unit tests are written for individual units/components of a system. The primary goal is to isolate pieces of testable code in the system, and determine whether they behave as expected. The unit or component may be a function, procedure, method or class. 
***
#### How to write unit tests
1. **Select a unit test framework:** Most languages already have a unit testing framework. The framework is a development tool, same as your editor and compiler. 
2. **Identify the testable units/components:** Ideally _ALL_ classes in a system should be testable. The test cases identified should focus on test that impact the behaviour of the system.
3. **Create test files in same directory as the source file:** Test files should reside in the same directory as the source file with `.spec.js` suffix. This is to ensure easy accessibility of the source file from the test file. 
4. **Implement the test:** A unit test basically should test one method, provide arguments to the method, test that the result is as expected.Ensure that each test case is independent of each other. This means if a method depends on a database, the test case should not interact with the database to test the method. Instead, an abstract interface should be created around the database connection and implemented with the mock object.
5. **Run the tests:** Ensure that the tests can be run on any environment locally, and on a CI. The tests are usually released into the code repository along with the code they test. Code without tests should not be released.

#### Example:

**HelloWorld.py**

```python
class Hello:
     def __init__(self):
         self.message = 'Hello world'
         print self.message
```

**HelloWorldTest.py**

```python
import unittest
from HelloWorld import Hello

class MyTestCase(unittest.TestCase):
    def test_default_hello_set(self):
        hello = Hello()
        # failing test
        self.assertEqual(hello.message, 'Hello world!')

if __name__ == '__main__':
    unittest.main()
```
*** 
#### Benefits of unit tests
* **Reusable code:** Unit testing is easier when the code is modular. So this makes code easier to reuse.
* **Easier debugging:** Only the latest changes need to be debugged when a test fails.
* **Reliability:** The code is more reliable, thereby increasing confidence in the use of the application.
***
### Acceptance Testing
***
Acceptance testing is a level of testing that determines whether the requirements of a specification are met. An acceptance test describes the behavior of the system, normally expressed in the form of a scenario. In many cases the scenarios are automated by the use of a software tool. An acceptance test will have a binary result, either a pass or a fail. A failure suggests a defect in the system.
***
#### How to write acceptance tests
Acceptance tests are created from user stories. User stories created from an iteration or sprint planning will be translated into acceptance tests.
1. **Write an acceptance test plan:** The test plan will serve as a guide to testing. It also serves as a valuable record of what testing was done.Besides that it will get you thinking about steps and features you may need to include in the system to enable testing
2. **Collect user stories:** A User Story is a description of an objective a person should be able to achieve, or a feature that a person should be able to utilize, when using a software application
3. **Select an automation tool:** There are numerous commercial tools and a few open source tools available developed by the agile development community. It’s important to pick the right tool for testing. Some automated testing tools support only one type of application, for instance, only Java or .NET applications, while other tools support more application types. In general, the best option is to choose a tool that supports all development tools currently used in the organization, or all tools that you plan on using in the near future.
4. **Implement the tests:** Write the tests and ensure they can run on a CI as well. They will serve as regression tests once completed. Remember, a user story is not considered complete until it has passed its acceptance tests.
***
#### Example:

#### Benefits of acceptance tests
* Encourages collaboration between developers and users because they entail that business requirements should be met.
* Provides clear and unambiguous contract between users and developers; a product with passing acceptance tests is considered adequate
* Decreases the chance and severity of new defects
