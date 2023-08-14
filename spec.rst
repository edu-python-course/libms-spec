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

User roles
==========

.. note::
    It is essential for developers to consider security aspects when
    implementing these roles to ensure that users cannot perform actions
    outside their designated role's scope.

Admins
------

Superusers have full control over the entire system. They can access and manage
all functionalities provided by the Django admin site.

Admins can create, modify, or delete any record within the application.

.. rubric:: Capabilities

*   Access Django admin site

Librarians
----------

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
-------

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
---------

Anonymous users are visitors who haven't logged into the system.

.. rubric:: Capabilities

*   Browse the books' catalogue
*   View details of a book
*   Search books
*   Access to the registration or login pages
