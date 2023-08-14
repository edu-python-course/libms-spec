###############################################################################
                   Library Management System - Specification
###############################################################################

The Library Management System is a Django-based web application that offers a
digital platform for managing and interacting with a library's collection. It
streamlines the processes of book lending, returns, and inventory management,
while ensuring easy access for users and librarians alike. Through this system,
users can search for available books, place borrowing requests, and manage
their borrowed items. Librarians, on the other hand, oversee the addition,
update, and removal of book records, and can approve or decline borrowing
requests. The system also supports diverse user roles, from regular members to
staff and administrators, each with distinct capabilities.

A well-structured user interface ensures that all functionalities are easily
accessible, making the library management process seamless and efficient. This
project aims to merge traditional library functions with modern technological
innovations, promoting efficiency and an enhanced user experience.

**********
User roles
**********

.. note::
    It is essential for developers to consider security aspects when
    implementing these roles to ensure that users cannot perform actions
    outside their designated role's scope.

Admins
======

Superusers have full control over the entire system. They can access and manage
all functionalities provided by the Django admin site.

Admins can create, modify, or delete any record within the application.

.. rubric:: Capabilities

*   Access Django admin site

Librarians
==========

Staff personnel are in charge of day-to-day operations within the library.
They can add new books to the library's collection, manage existing books'
details, and accept or decline borrow requests from regular users.

.. rubric:: Capabilities

*   Management of the library catalogue

    *   Add, modify or delete authors
    *   Add, modify or delete genres
    *   Add, modify or delete books' details

*   Review borrow requests from regular users
*   Accept or decline borrow requests
*   Register returned books and update their status

Regular
=======

Regular registered users are the members of the library. They can search books,
request to borrow them, and are responsible for returning borrowed books on
time.

.. rubric:: Capabilities

*   Browse the books' catalogue
*   View details of a book
*   Search books
*   Request to borrow available books
*   Check due date for returned books
*   Return borrowed books

Anonymous
=========

Anonymous users are visitors who haven't logged into the system.

.. rubric:: Capabilities

*   Browse the books' catalogue
*   View details of a book
*   Search books
*   Access to the registration or login pages

******
Models
******

Author model
============

The ``Author`` model captures the details of the person, or group responsible
for creating the book.

Attributes
----------

:Name:
    The full name of the author. This should be limited to 255 characters.

:Bio:
    A brief biography or description of the author.

Genre model
===========

The ``Genre`` model represents the category or type of content a book belongs
to, such as "Fiction", "Non-fiction", "Science", "Romance" etc. Since a book
can belong to more than one category at a time, it is implemented as
a dedicated model.

Attributes
----------

:Name: The name of the genre.

Book model
==========

The ``Book`` model represents a physical book in the library's collection. Each
book record captures essential bibliographic information, as well as details
related to its current status in the library (e.g. available, borrowed etc.).

Attributes
----------

:Title:
    The name or title of the book (255 characters or fewer).

:Summary:
    A brief description or synopsis of the book.

:ISBN:
    International Standard Book Number, a unique identifier for books.

:Status:
    Current status of the book.

    * Available
    * Awaiting approval
    * Borrowed
    * Approve returned

:Published date:
    The date when the book was published.

:Publisher:
    The entity or company responsible for the production and distribution
    of the book.

:Genre(s):
    The category or type of content the book falls under (e.g., Fiction,
    Non-Fiction, Science).

:Author(s):

    The name of the person or group responsible for creating the content of
    the book.

:Borrower:
    The user who has currently borrowed the book, if applicable.

Borrow request model
====================

The ``BorrowRequest`` model captures the intent of a user to borrow a specific
book. It tracks the status of the request, whether it's pending, approved,
declined, or completed.

Attributes
----------

:Book:
    A reference to a book to be borrowed.

:Borrower:
    A reference to a user who wants to borrow a book.

:Status:
    The current status of the request.

    * pending
    * approved
    * declined
    * complete

:Request date:
    The date the borrow request was made. This is required.

:Approval date:
    The date the request was approved, if applicable. Optional.

:Due date:
    The date by which the book should be returned.
    Optional, should only be set if the request if approved.

:Complete date:
    The date the book was returned and the borrowing request was completed.
    Optional, should only be set if the request has reached the "Complete"
    status.

Relationships
=============

Book to author
--------------

Each ``Author`` can be associated with multiple ``Books``, and each ``Book``
can have **one or more** ``authors``. This establishes a **many-to-many**
relationship between the two entities.

Book to genre
-------------

Each ``Genre`` can be associated with multiple ``Books``, and vice-versa.
However a ``Book`` might not belong to any genre. This relationship between
``Genre`` and ``Book`` is still considered **many-to-many**, but with the
possibility of zero genres for a book.

Book to borrow request
----------------------

Each ``Book`` can be associated with multiple ``BorrowRequests``, but each
``BorrowRequest`` refers only one ``book``, forming a **many-to-one**
relationship.

User to borrow request
----------------------

Each ``User`` can make multiple ``BorrowRequests``, but each ``BorrowRequest``
refers to only one book, forming a **many-to-one** relationship.

User to book (as a borrower)
----------------------------

A ``Book`` may have been borrowed by a ``User``, but at any given time, each
``Book`` can be borrowed by one user only. This forms a **one-to-one**
relationship.
