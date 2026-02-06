#include <iostream>
#include <vector>
#include <string>
using namespace std;

/* =======================
   Book Class
   ======================= */
class Book {
private:
    int bookId;
    string title;
    string author;
    bool isBorrowed;

public:
    Book(int id, string t, string a) {
        bookId = id;
        title = t;
        author = a;
        isBorrowed = false;
    }

    int getId() const {
        return bookId;
    }

    string getTitle() const {
        return title;
    }

    string getAuthor() const {
        return author;
    }

    bool getStatus() const {
        return isBorrowed;
    }

    void borrowBook() {
        isBorrowed = true;
    }

    void returnBook() {
        isBorrowed = false;
    }

    void display() const {
        cout << "ID: " << bookId
             << " | Title: " << title
             << " | Author: " << author
             << " | Status: " << (isBorrowed ? "Borrowed" : "Available")
             << endl;
    }
};

/* =======================
   User Class
   ======================= */
class User {
private:
    int userId;
    string name;
    vector<int> borrowedBooks;

public:
    User(int id, string n) {
        userId = id;
        name = n;
    }

    int getId() const {
        return userId;
    }

    string getName() const {
        return name;
    }

    void borrowBook(int bookId) {
        borrowedBooks.push_back(bookId);
    }

    void returnBook(int bookId) {
        for (auto it = borrowedBooks.begin(); it != borrowedBooks.end(); it++) {
            if (*it == bookId) {
                borrowedBooks.erase(it);
                break;
            }
        }
    }

    void display() const {
        cout << "User ID: " << userId << " | Name: " << name << endl;
    }
};

/* =======================
   Library Class
   ======================= */
class Library {
private:
    vector<Book> books;
    vector<User> users;

public:
    void addBook(Book b) {
        books.push_back(b);
        cout << "Book added successfully.\n";
    }

    void removeBook(int bookId) {
        for (auto it = books.begin(); it != books.end(); it++) {
            if (it->getId() == bookId) {
                books.erase(it);
                cout << "Book removed successfully.\n";
                return;
            }
        }
        cout << "Book not found.\n";
    }

    void addUser(User u) {
        users.push_back(u);
        cout << "User added successfully.\n";
    }

    void searchByTitle(string title) const {
        bool found = false;
        for (const auto &b : books) {
            if (b.getTitle() == title) {
                b.display();
                found = true;
            }
        }
        if (!found) {
            cout << "No book found with that title.\n";
        }
    }

    void displayBooks() const {
        if (books.empty()) {
            cout << "No books available.\n";
            return;
        }
        for (const auto &b : books) {
            b.display();
        }
    }

    void borrowBook(int bookId, int userId) {
        for (auto &b : books) {
            if (b.getId() == bookId && !b.getStatus()) {
                for (auto &u : users) {
                    if (u.getId() == userId) {
                        b.borrowBook();
                        u.borrowBook(bookId);
                        cout << "Book borrowed successfully.\n";
                        return;
                    }
                }
            }
        }
        cout << "Borrowing failed (book unavailable or user not found).\n";
    }

    void returnBook(int bookId, int userId) {
        for (auto &b : books) {
            if (b.getId() == bookId && b.getStatus()) {
                for (auto &u : users) {
                    if (u.getId() == userId) {
                        b.returnBook();
                        u.returnBook(bookId);
                        cout << "Book returned successfully.\n";
                        return;
                    }
                }
            }
        }
        cout << "Return failed.\n";
    }
};

/* =======================
   Test Cases (Main)
   ======================= */
int main() {
    Library library;

    // Adding books
    library.addBook(Book(1, "C++ Basics", "Bjarne Stroustrup"));
    library.addBook(Book(2, "Object Oriented Programming", "James Gosling"));

    // Adding users
    library.addUser(User(101, "Eric"));
    library.addUser(User(102, "Kyla"));

    cout << "\n--- All Books ---\n";
    library.displayBooks();

    cout << "\n--- Search Book ---\n";
    library.searchByTitle("C++ Basics");

    cout << "\n--- Borrow Book (Positive Case) ---\n";
    library.borrowBook(1, 101);

    cout << "\n--- Borrow Book (Negative Case) ---\n";
    library.borrowBook(1, 102);

    cout << "\n--- Return Book ---\n";
    library.returnBook(1, 101);

    cout << "\n--- Final Book List ---\n";
    library.displayBooks();

    return 0;
}
