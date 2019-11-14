![Image of library modules](https://raw.githubusercontent.com/ndrone/dev.to/master/blog-posts/repositories-are-not-just-for-db/assets/libraryModules.png)

Let's ignore the contact-core-test module for now. It contains a base test class. Each ContactRepository implementation test class can extend the base test class, and then only needs to write tests based on their implementation, but should pass all tests in the base class. I may turn that into a blog post of its own.

The contact-core module. This is a small simple module that contains the contact object. The repository interface for the contact object. The exception class for that object, and the validator class. The validator class just validates that the inputs specified in the methods of the interface adhere to the specifications of the javadocs of the interface.

```ts
// code/ContactRepository.java
```

The contact-file, contact-http, and contact-jdbc are all modules that implement the `ContactRepository`. So an application then can bring in the specific technology implementation and build an application based on the interface. 

##A hypothetical scenario

As a product team, we are spinning up a contact service. We've requested a database, but that won't be ready for weeks, and the boss wants it done by next week. Well, we still can write that service. The rest application is written to the `ContactRepository` interface. We bring in the contact-file dependency for the short term while we are waiting on our database to be complete. Once the database is complete we then can swap out the contact-file dependency for the jdbc one, only changing enough code to connect to the database. Then file contents are moved to the database after deployment. The boss shows off your work to another team that wants access to the contact info. Except they can't get past the firewall use the contact-jdbc implementation. Not that you wanted them to. So you create the HTTP implementation to your applications URL endpoints. Then they just need to write code to the interface you just did, but leverage HTTP to get the same data you got from the JDBC implementation. The good news is if your API change, you can control the HTTP implementation too, so you can save your customer the headache of handling the versioning, just give them the new HTTP jar with the versioned changes in it.