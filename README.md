#include <iostream>
#include <string>
#include <vector>
#include <unordered_set>
#include <algorithm>
#include <limits>

using namespace std;

struct Book {
    int id;
    string title;
    string author;
    bool available;
    
    Book() : id(0), available(true) {}
    Book(int i, const string& t, const string& a) : id(i), title(t), author(a), available(true) {}
    
    void print() const {
        cout << "ID: " << id << " | " << title << " | " << author
             << " | " << (available ? "Available" : "Borrowed") << endl;
    }
};

struct User {
    int id;
    string name;
    unordered_set<int> borrowed;
    
    User() : id(0) {}
    User(int i, const string& n) : id(i), name(n) {}
    
    bool hasBook(int bookId) const {
        return borrowed.count(bookId) > 0;
    }
    
    bool addBook(int bookId) {
        return borrowed.insert(bookId).second;
    }
    
    bool removeBook(int bookId) {
        return borrowed.erase(bookId) > 0;
    }
    
    bool empty() const {
        return borrowed.empty();
    }
    
    void print() const {
        cout << "User[" << id << "] " << name << " | Borrowed: " << borrowed.size() << endl;
    }
};

class Library {
private:
    vector<Book> books;
    vector<User> users;
    
    Book* findBook(int bookId) {
        for (auto& b : books) {
            if (b.id == bookId) return &b;
        }
        return nullptr;
    }
    
    User* findUser(int userId) {
        for (auto& u : users) {
            if (u.id == userId) return &u;
        }
        return nullptr;
    }

public:
    bool addBook(const Book& b) {
        if (findBook(b.id)) {
            cout << "Book ID exists." << endl;
            return false;
        }
        books.push_back(b);
        return true;
    }
    
    bool addUser(const User& u) {
        if (findUser(u.id)) {
            cout << "User ID exists." << endl;
            return false;
        }
        users.push_back(u);
        return true;
    }
    
    void listBooks() const {
        cout << "\n--- Books ---" << endl;
        if (books.empty()) {
            cout << "(none)" << endl;
        } else {
            for (const auto& b : books) {
                b.print();
            }
        }
    }
    
    void listUsers() const {
        cout << "\n--- Users ---" << endl;
        if (users.empty()) {
            cout << "(none)" << endl;
        } else {
            for (const auto& u : users) {
                u.print();
            }
        }
    }
    
    bool borrowBook(int userId, int bookId) {
        User* u = findUser(userId);
        if (!u) {
            cout << "User not found." << endl;
            return false;
        }
        
        Book* b = findBook(bookId);
        if (!b) {
            cout << "Book not found." << endl;
            return false;
        }
        
        if (!b->available) {
            cout << "Book already borrowed." << endl;
            return false;
        }
        
        if (u->hasBook(bookId)) {
            cout << "User already has this book." << endl;
            return false;
        }
        
        u->addBook(bookId);
        b->available = false;
        cout << "Book borrowed successfully!" << endl;
        return true;
    }
    
    bool returnBook(int userId, int bookId) {
        User* u = findUser(userId);
        if (!u) {
            cout << "User not found." << endl;
            return false;
        }
        
        Book* b = findBook(bookId);
        if (!b) {
            cout << "Book not found." << endl;
            return false;
        }
        
        if (!u->hasBook(bookId)) {
            cout << "User doesn't have this book." << endl;
            return false;
        }
        
        u->removeBook(bookId);
        b->available = true;
        cout << "Book returned successfully!" << endl;
        return true;
    }
};

int main() {
    Library lib;
    
    lib.addBook(Book(1, "C++ Programming", "Stroustrup"));
    lib.addBook(Book(2, "Clean Code", "Martin"));
    lib.addBook(Book(3, "Algorithms", "Cormen"));
    
    lib.addUser(User(1, "Alice"));
    lib.addUser(User(2, "Bob"));
    
    int choice;
    while (true) {
        cout << "\n=== Library Management System ===" << endl;
        cout << "1. List Books" << endl;
        cout << "2. List Users" << endl;
        cout << "3. Borrow Book" << endl;
        cout << "4. Return Book" << endl;
        cout << "0. Exit" << endl;
        cout << "Enter choice: ";
        
        cin >> choice;
        
        if (choice == 0) {
            cout << "Goodbye!" << endl;
            break;
        }
        
        switch (choice) {
            case 1:
                lib.listBooks();
                break;
            case 2:
                lib.listUsers();
                break;
            case 3: {
                int userId, bookId;
                cout << "Enter User ID: ";
                cin >> userId;
                cout << "Enter Book ID: ";
                cin >> bookId;
                lib.borrowBook(userId, bookId);
                break;
            }
            case 4: {
                int userId, bookId;
                cout << "Enter User ID: ";
                cin >> userId;
                cout << "Enter Book ID: ";
                cin >> bookId;
                lib.returnBook(userId, bookId);
                break;
            }
            default:
                cout << "Invalid choice!" << endl;
        }
    }
    
    return 0;
}
