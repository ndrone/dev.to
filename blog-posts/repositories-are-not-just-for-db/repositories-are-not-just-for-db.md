---
published: true
title: "Repositories are not just for Databases"
cover_image: "https://raw.githubusercontent.com/ndrone/dev.to/master/blog-posts/repositories-are-not-just-for-db/assets/cover.png"
description: "A reminder that the repository (DAO) layer is not just for databases."
tags: oop
series:
canonical_url: https://dev.to/ndrone/repositories-are-not-just-for-databases-4a85
---

![Image of library modules](https://raw.githubusercontent.com/ndrone/dev.to/master/blog-posts/repositories-are-not-just-for-db/assets/libraryModules.png)

Let's ignore the contact-core-test module for now. It contains a base test class. Each ContactRepository implementation test class can extend the base test class, and then only needs to write tests based on their implementation, but should pass all tests in the base class. I may turn that into a blog post of its own.

The contact-core module. This is a small simple module that contains the contact object. The repository interface for the contact object. The exception class for that object, and the validator class. The validator class just validates that the inputs specified in the methods of the interface adhere to the specifications of the javadocs of the interface.

```ts
package org.example;

/**
 * A simple ContactRepository. Any implementation of this interface should
 * leverage {@link ContactRepositoryValidator} to validate the overriding methods
 * arguments. The implementations Tests classes should extend ContactRepositoryTests
 *
 * @see contact-core-test module
 */
public interface ContactRepository {

  /**
   * Search for the contact with the existing <code>id</code>
   *
   * @param id of the contact to be searched for
   * @return <code>contact</code> maybe <code>null</code> if <code>contact</code>
   * with the <code>id</code> doesn't exist
   * @throws IllegalArgumentException if <code>id</code> is <code>null</code>
   * @throws ContactException         if an error has occurred
   */
  Contact findContact(Integer id) throws IllegalArgumentException, ContactException;

  /**
   * Searching for <code>contact</code> that exactly matches the parameters
   * passed into the method.
   *
   * @param firstName to be exactly searched with
   * @param lastName  to be exactly searched with
   * @return a collection of <code>contact</code> that match the parameters
   * @throws IllegalArgumentException if both parameters are <code>null</code>
   * @throws ContactException         if an error has occurred
   */
  Iterable<Contact> findContacts(String firstName, String lastName)
      throws IllegalArgumentException, ContactException;

  /**
   * Saves a new or updated contact information. If a <code>contact</code> is passed with the
   * <code>id</code> is <code>null</code>. The contact will be treated as a new contact, and
   * an <code>id</code> will be generated for it. If an <code>contact</code> has an existing
   * <code>id</code> the <code>contact</code> with be updated.
   *
   * @param contact to be saved or updated
   * @return saved <code>contact</code>
   * @throws IllegalArgumentException if a <code>contact</code> with an existing <code>id</code>
   *                                  can not be found.
   * @throws ContactException         if an error has occurred
   */
  Contact saveContact(Contact contact) throws IllegalArgumentException, ContactException;
}
```

The contact-file, contact-http, and contact-jdbc are all modules that implement the `ContactRepository`. So an application then can bring in the specific technology implementation and build an application based on the interface. 

##A hypothetical scenario

As a product team, we are spinning up a contact service. We've requested a database, but that won't be ready for weeks, and the boss wants it done by next week. Well, we still can write that service. The rest application is written to the `ContactRepository` interface. We bring in the contact-file dependency for the short term while we are waiting on our database to be complete. Once the database is complete we then can swap out the contact-file dependency for the jdbc one, only changing enough code to connect to the database. Then file contents are moved to the database after deployment. The boss shows off your work to another team that wants access to the contact info. Except they can't get past the firewall use the contact-jdbc implementation. Not that you wanted them to. So you create the HTTP implementation to your applications URL endpoints. Then they just need to write code to the interface you just did, but leverage HTTP to get the same data you got from the JDBC implementation. The good news is if your API change, you can control the HTTP implementation too, so you can save your customer the headache of handling the versioning, just give them the new HTTP jar with the versioned changes in it.