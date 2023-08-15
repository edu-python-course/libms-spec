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

:Available:
    Current available status of the book. Either ``True`` or ``False``.
    Books are available by default.

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

    -   pending
    -   approved
    -   collected
    -   complete
    -   declined

    The status constants should be encapsulated within the ``BorrowRequest``
    model.

    .. code-block:: python

        class BorrowRequestModel(models.Model):
            ...
            PENDING = 1
            APPROVED = 2
            COLLECTED = 3
            COMPLETE = 4
            DECLINED = 5
            ...

:Overdue:
    A flag indicating if a borrow request was overdue.

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

.. mermaid:: ./mermaid/models.mmd
    :align: center
    :caption: Entity relationship diagram
    :alt: entity relationship diagram

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

*******************
Views and templates
*******************

Admin site
==========

Only admins are permitted to use admin site.
Librarians should use their dedicated views to perform operations.
Admin site provides the full control over the entire system.

Authentication
==============

Login
-----

.. rubric:: Template

A form with fields for entering a username and a password, and a submit button.

.. rubric:: View logic

Validate entered credentials and authenticate the user. In case of incorrect
credentials, display an error message.

Registration
------------

.. rubric:: Template

A form for new user registration. **Required** fields are:

-   username
-   first name
-   last name
-   password
-   confirm password

.. rubric:: View logic

Upon submission, the view should validate the provided data:

-   Check if username is already taken.
-   Ensure that password and confirm password fields match.
-   Save the user's data if all validations pass.
    Otherwise, display appropriate error messages.

When saving passwords in the database, always store them in a hashed format,
never in plain text.

.. hint::
    Django's built-in ``User`` model and authentication
    system handle password hashing by default, which is a big plus.

Borrowing History
=================

This view is available only for authenticated users.

.. rubric:: Template

Users can see the list of **all** their borrows requests, regardless of
approve/decline status.

.. rubric:: View logic

-   Authenticated users can see only their own borrow requests.
-   Librarians and admins can access borrows requests list for any user,
    registered in the library system.

Library collection
==================

These views are generally available for all (including anonymous) users.
The functionality for authenticated users include books borrowing.

Books list
----------

.. rubric:: Template

A list (or table) of books. Each book entry should provide information:

-   title
-   author(s)
-   summary (truncated if too long, 30 words or fewer)
-   availability status (either **Available** or **Unavailable**)

.. rubric:: View logic

#.  Querying the data

    -   Fetch **all** books from the database

#.  Checking availability status (visual mark)

    -   For each book, determine its availability status.
        Check ``Book.available`` flag.

    .. note::
        A user can still create a borrowing request, regardless of the book
        availability status. This is only the visual mark.

Detail view
-----------

.. rubric:: Template

The detailed view of a specific book, presenting comprehensive information:

-   **Title**: Display the book's title prominently.
-   **Author(s)**: List all associated authors.
-   **Summary**: A full summary or description of the book.
-   **ISBN**: The International Standard Book Number.
-   **Published Date**: When the book was published.
-   **Publisher**: The name of the entity or company responsible for publishing
    the book.
-   **Genre(s)**: List all associated genres.
-   **Action Buttons** (For authenticated users only):
    -   **Borrow Request Button**: If the user has no associated borrow request
        they can create a new one.
    -   **Gather Book Button**: If the user's borrow request for this book has
        been approved, allow the user to confirm they've picked up the book.

.. rubric:: View logic

#.  **Fetching Book Details**:

    -   Use the book's identifier (usually a primary key) to fetch its details
        from the database.

#.  **Checking User Authentication**:

    -   Determine if a user is authenticated. If they are, display the
        appropriate action buttons based on the book's availability status
        and any existing borrow requests by the user.

#.  **Handling Borrow Requests**:

    -   If an authenticated user clicks on the "Borrow Request" button:

        #.  Create a new borrow request in the ``BorrowRequest`` model with
            status "pending".
        #.  Update the book's status to "Awaiting approval".
        #.  Redirect the user to a confirmation page or display a message
            indicating the request has been made.

#.  **Handling Book Gathering**:

    -   If an authenticated user has an approved borrow request and clicks
        the "Gather Book" button:

        #.  Update the book's status to "Borrowed".
        #.  Update the borrow request's status to "complete".
        #.  Redirect the user to a confirmation page or display a message
            indicating they've picked up the book.

.. note::
    Always ensure the user's actions are authenticated and authorized.
    This prevents unauthorized borrow requests or gathering of books.
    Also, consider adding error handling to manage cases where the book's
    details cannot be fetched or any other unexpected issues.

Books management
================

These views are available only for staff (librarians and admins).

Book creation
-------------

.. todo:

Borrow requests
---------------

.. todo:
    a list of borrow request with information about current status, borrower
    and the book
    functions:
        for new requests each entry has a button to approve or decline request
        approved requests should have due date set
        for approved requests complete button is available
        if complete status is set after the due date - overdue flag should be
        set.
