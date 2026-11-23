# Banking-system
Banking system calculating withdrawals made by clients
#include <iostream>
#include <string>
#include <limits>
using namespace std;

const int USER_COUNT = 3;

// Demonstration account details. These are fictional sample users.
string names[USER_COUNT] = {"Gift Mvimbi", "Sbahle Mokoena", "Amara Ndlovu"};
string accountNumbers[USER_COUNT] = {"1001001", "1001002", "1001003"};
string pins[USER_COUNT] = {"1234", "2345", "3456"};
double balances[USER_COUNT] = {2500.00, 1800.00, 3200.00};

// Reads a whole-number choice and safely handles letters or other invalid input.
int readInteger(string prompt) {
    int value;
    while (true) {
        cout << prompt;
        cin >> value;

        if (!cin.fail()) {
            return value;
        }

        cout << "Invalid input. Please enter a number.\n";
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }
}

// Reads a money amount and rejects invalid or non-numeric input.
double readAmount(string prompt) {
    double amount;
    while (true) {
        cout << prompt;
        cin >> amount;

        if (!cin.fail()) {
            return amount;
        }

        cout << "Invalid amount. Please enter numbers only.\n";
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }
}

// Searches the account-number array and returns the matching position, or -1.
int findAccount(string accountNumber) {
    for (int i = 0; i < USER_COUNT; i++) {
        if (accountNumbers[i] == accountNumber) {
            return i;
        }
    }
    return -1;
}

// Checks the account number and PIN before allowing access.
int login() {
    string enteredAccount, enteredPin;

    cout << "========== GCC BANK ==========" << endl;
    cout << "DEMO ACCOUNTS FOR TESTING" << endl;
    cout << "1001001 / 1234, 1001002 / 2345, 1001003 / 3456" << endl;

    for (int attempt = 1; attempt <= 3; attempt++) {
        cout << "\nEnter account number: ";
        cin >> enteredAccount;
        cout << "Enter PIN: ";
        cin >> enteredPin;

        int userIndex = findAccount(enteredAccount);

        if (userIndex != -1 && pins[userIndex] == enteredPin) {
            cout << "\nWelcome, " << names[userIndex] << "!" << endl;
            return userIndex;
        } else {
            cout << "Incorrect account number or PIN. Attempts left: "
                 << 3 - attempt << endl;
        }
    }

    return -1;
}

// Displays the banking options and returns the user's menu choice.
int displayMenu() {
    cout << "\n========== GCC BANK MENU ==========" << endl;
    cout << "1. Deposit" << endl;
    cout << "2. Withdraw" << endl;
    cout << "3. Send Cash" << endl;
    cout << "4. Check Balance" << endl;
    cout << "5. Exit" << endl;
    return readInteger("Enter choice: ");
}

// Adds a positive amount to the selected user's balance.
void deposit(int userIndex, double accountBalances[]) {
    double amount = readAmount("Enter amount to deposit: R");
    if (amount > 0) {
        accountBalances[userIndex] += amount;
        cout << "Deposit successful." << endl;
        cout << "New balance: R" << accountBalances[userIndex] << endl;
    } else {
        cout << "Deposit rejected. The amount must be more than R0." << endl;
    }
}

// Removes money only when the amount is positive and the balance is enough.
void withdraw(int userIndex, double accountBalances[]) {
    double amount = readAmount("Enter amount to withdraw: R");
    if (amount <= 0) {
        cout << "Withdrawal rejected. Enter an amount greater than R0." << endl;
    } else if (amount > accountBalances[userIndex]) {
        cout << "Insufficient funds. Withdrawal was not completed." << endl;
    } else {
        accountBalances[userIndex] -= amount;
        cout << "Withdrawal successful." << endl;
        cout << "New balance: R" << accountBalances[userIndex] << endl;
    }
}

// Transfers money between two existing accounts and updates both balances.
void sendCash(int senderIndex, double accountBalances[]) {
    string recipientNumber;
    cout << "Enter recipient account number: ";
    cin >> recipientNumber;

    int recipientIndex = findAccount(recipientNumber);

    if (recipientIndex == -1) {
        cout << "Transfer failed. The recipient account does not exist." << endl;
        return;
    }

    if (recipientIndex == senderIndex) {
        cout << "Transfer failed. You cannot send cash to your own account." << endl;
        return;
    }

    double amount = readAmount("Enter amount to send: R");

    if (amount <= 0) {
        cout << "Transfer rejected. The amount must be more than R0." << endl;
    } else if (amount > accountBalances[senderIndex]) {
        cout << "Transfer failed. Insufficient funds." << endl;
    } else {
        accountBalances[senderIndex] -= amount;
        accountBalances[recipientIndex] += amount;
        cout << "Cash sent successfully to " << names[recipientIndex] << "." << endl;
        cout << "Your new balance: R" << accountBalances[senderIndex] << endl;
    }
}

// Displays the selected user's current balance.
void checkBalance(int userIndex, double accountBalances[]) {
    cout << "\nAccount holder: " << names[userIndex] << endl;
    cout << "Account number: " << accountNumbers[userIndex] << endl;
    cout << "Available balance: R" << accountBalances[userIndex] << endl;
}

int main() {
    int userIndex = login();

    if (userIndex == -1) {
        cout << "\nAccess denied. The program will close." << endl;
        return 0;
    }

    int choice;

    // The menu repeats until the user chooses option 5.
    do {
        choice = displayMenu();

        if (choice == 1) {
            deposit(userIndex, balances);
        } else if (choice == 2) {
            withdraw(userIndex, balances);
        } else if (choice == 3) {
            sendCash(userIndex, balances);
        } else if (choice == 4) {
            checkBalance(userIndex, balances);
        } else if (choice == 5) {
            cout << "Thank you for using GCC Bank. Goodbye!" << endl;
        } else {
            cout << "Invalid menu choice. Please choose from 1 to 5." << endl;
        }

    } while (choice != 5);

    return 0;
}
