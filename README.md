# Assignment-one
oop1
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

// ---------------- Book Class ----------------
class Book {
private:
    string title;
    string author;
    string isbn;
    bool isAvailable;

public:
    Book(string t, string a, string i)
        : title(t), author(a), isbn(i), isAvailable(true) {}

    string getTitle() { return title; }
    string getAuthor() { return author; }
    string getISBN() { return isbn; }
    bool getAvailability() { return isAvailable; }

    void borrowBook() {
        if (isAvailable) {
            isAvailable = false;
            cout << "Book borrowed successfully: " << title << endl;
        } else {
            cout << "Book is already borrowed!" << endl;
        }
    }

    void returnBook() {
        isAvailable = true;
        cout << "Book returned successfully: " << title << endl;
    }

    void display() {
        cout << "Title: " << title
             << ", Author: " << author
             << ", ISBN: " << isbn
             << ", Available: " << (isAvailable ? "Yes" : "No") << endl;
    }
};

// ---------------- User Class ----------------
class User {
private:
    string name;
    vector<string> borrowedBooks; // store ISBN of borrowed books

public:
    User(string n) : name(n) {}

    string getName() { return name; }

    void borrowBook(Book &book) {
        if (book.getAvailability()) {
            book.borrowBook();
            borrowedBooks.push_back(book.getISBN());
        } else {
            cout << "Sorry " << name << ", book not available!" << endl;
        }
    }

    void returnBook(Book &book) {
        auto it = find(borrowedBooks.begin(), borrowedBooks.end(), book.getISBN());
        if (it != borrowedBooks.end()) {
            book.returnBook();
            borrowedBooks.erase(it);
        } else {
            cout << "This user didn’t borrow that book!" << endl;
        }
    }

    void displayBorrowedBooks() {
        cout << name << " borrowed books: ";
        for (auto &isbn : borrowedBooks) cout << isbn << " ";
        cout << endl;
    }
};

// ---------------- Library Class ----------------
class Library {
private:
    vector<Book> books;

public:
    void addBook(Book book) {
        books.push_back(book);
        cout << "Book added successfully!" << endl;
    }

    void removeBook(string isbn) {
        auto it = remove_if(books.begin(), books.end(),
            [&](Book &b) { return b.getISBN() == isbn; });

        if (it != books.end()) {
            books.erase(it, books.end());
            cout << "Book removed successfully!" << endl;
        } else {
            cout << "Book not found!" << endl;
        }
    }

    Book* searchBook(string title) {
        for (auto &b : books) {
            if (b.getTitle() == title) {
                return &b;
            }
        }
        cout << "Book not found!" << endl;
        return nullptr;
    }

    void displayBooks() {
        cout << "\nLibrary Books:\n";
        for (auto &b : books) {
            b.display();
        }
        cout << endl;
    }
};

// ---------------- Main Program ----------------
int main() {
    Library lib;
    User user1("Alice");

    // Add books
    lib.addBook(Book("C++ Basics", "Bjarne Stroustrup", "111"));
    lib.addBook(Book("OOP Concepts", "James Gosling", "222"));
    lib.addBook(Book("Data Structures", "Donald Knuth", "333"));

    lib.displayBooks();

    // Search and borrow
    Book* book1 = lib.searchBook("C++ Basics");
    if (book1) user1.borrowBook(*book1);

    // Try borrowing again (should fail)
    if (book1) user1.borrowBook(*book1);

    // Return book
    if (book1) user1.returnBook(*book1);

    // Display borrowed books
    user1.displayBorrowedBooks();

    // Remove a book
    lib.removeBook("222");

    lib.displayBooks();

    return 0;
}
