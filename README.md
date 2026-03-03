# Digital-Attendance-System
This system is designed to replace the traditional paper-based method of taking attendance during hour-based lectures. This system provides a simple, efficient, and reliable digital solution that allows lecturers to manage student records and attendance sessions electronicall.

#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <sstream>

using namespace std;

/* =========================
        STUDENT CLASS
========================= */
class Student {
public:
    string indexNumber;
    string name;

    Student() {}

    Student(string idx, string nm) {
        indexNumber = idx;
        name = nm;
    }
};

/* =========================
     LOAD STUDENTS FROM FILE
========================= */
vector<Student> loadStudents() {
    vector<Student> students;
    ifstream file("students.txt");

    if (!file) {
        return students; // return empty if file doesn't exist
    }

    string line;
    while (getline(file, line)) {
        if (line.empty()) continue;

        stringstream ss(line);
        string idx, name;

        getline(ss, idx, ',');
        getline(ss, name);

        students.push_back(Student(idx, name));
    }

    file.close();
    return students;
}

/* =========================
     REGISTER STUDENT
========================= */
void registerStudent() {
    string idx, name;

    cout << "\nEnter Index Number: ";
    cin >> idx;
    cin.ignore();

    cout << "Enter Full Name: ";
    getline(cin, name);

    ofstream file("students.txt", ios::app);

    if (!file) {
        cout << "Error opening file!\n";
        return;
    }

    file << idx << "," << name << endl;
    file.close();

    cout << "\nStudent Registered Successfully!\n";
}

/* =========================
     VIEW STUDENTS
========================= */
void viewStudents() {
    vector<Student> students = loadStudents();

    if (students.empty()) {
        cout << "\nNo students registered yet!\n";
        return;
    }

    cout << "\n===== REGISTERED STUDENTS =====\n";

    for (size_t i = 0; i < students.size(); i++) {
        cout << students[i].indexNumber
             << " - "
             << students[i].name << endl;
    }
}

/* =========================
     SEARCH STUDENT
========================= */
void searchStudent() {
    vector<Student> students = loadStudents();
    string idx;

    cout << "\nEnter Index Number to Search: ";
    cin >> idx;

    bool found = false;

    for (size_t i = 0; i < students.size(); i++) {
        if (students[i].indexNumber == idx) {
            cout << "\nStudent Found:\n";
            cout << students[i].indexNumber
                 << " - "
                 << students[i].name << endl;
            found = true;
            break;
        }
    }

    if (!found)
        cout << "Student not found!\n";
}

/* =========================
     CREATE SESSION
========================= */
void createSession() {
    string courseCode, date;
    vector<Student> students = loadStudents();

    if (students.empty()) {
        cout << "\nNo students registered. Register students first!\n";
        return;
    }

    cout << "\nEnter Course Code: ";
    cin >> courseCode;

    cout << "Enter Date (YYYY_MM_DD): ";
    cin >> date;

    string fileName = "session_" + courseCode + "_" + date + ".txt";

    ofstream file(fileName.c_str());

    if (!file) {
        cout << "Error creating session file!\n";
        return;
    }

    for (size_t i = 0; i < students.size(); i++) {
        file << students[i].indexNumber
             << ","
             << students[i].name
             << ",Not Marked" << endl;
    }

    file.close();

    cout << "\nSession Created Successfully!\n";
}

/* =========================
     MARK ATTENDANCE
========================= */
void markAttendance() {
    string courseCode, date;

    cout << "\nEnter Course Code: ";
    cin >> courseCode;

    cout << "Enter Date (YYYY_MM_DD): ";
    cin >> date;

    string fileName = "session_" + courseCode + "_" + date + ".txt";

    ifstream file(fileName.c_str());

    if (!file) {
        cout << "Session file not found!\n";
        return;
    }

    vector<string> records;
    string line;

    while (getline(file, line)) {
        records.push_back(line);
    }
    file.close();

    ofstream outFile(fileName.c_str());

    for (size_t i = 0; i < records.size(); i++) {
        stringstream ss(records[i]);
        string idx, name, status;

        getline(ss, idx, ',');
        getline(ss, name, ',');
        getline(ss, status);

        cout << "\nMark attendance for "
             << name << " (" << idx << ")\n";
        cout << "1. Present\n2. Absent\n3. Late\nChoice: ";

        int choice;
        cin >> choice;

        while (choice < 1 || choice > 3) {
            cout << "Invalid choice. Enter 1-3: ";
            cin >> choice;
        }

        if (choice == 1) status = "Present";
        else if (choice == 2) status = "Absent";
        else status = "Late";

        outFile << idx << "," << name << "," << status << endl;
    }

    outFile.close();
    cout << "\nAttendance Marked Successfully!\n";
}

/* =========================
     VIEW REPORT
========================= */
void viewReport() {
    string courseCode, date;

    cout << "\nEnter Course Code: ";
    cin >> courseCode;

    cout << "Enter Date (YYYY_MM_DD): ";
    cin >> date;

    string fileName = "session_" + courseCode + "_" + date + ".txt";

    ifstream file(fileName.c_str());

    if (!file) {
        cout << "Session file not found!\n";
        return;
    }

    string line;
    int present = 0, absent = 0, late = 0;

    cout << "\n===== ATTENDANCE REPORT =====\n";

    while (getline(file, line)) {
        cout << line << endl;

        if (line.find("Present") != string::npos) present++;
        else if (line.find("Absent") != string::npos) absent++;
        else if (line.find("Late") != string::npos) late++;
    }

    file.close();

    cout << "\nSummary:\n";
    cout << "Present: " << present << endl;
    cout << "Absent : " << absent << endl;
    cout << "Late   : " << late << endl;
}

/* =========================
            MAIN
========================= */
int main() {
    int choice;

    do {
        cout << "\n==============================\n";
        cout << " DIGITAL ATTENDANCE SYSTEM\n";
        cout << "==============================\n";
        cout << "1. Register Student\n";
        cout << "2. View Students\n";
        cout << "3. Search Student\n";
        cout << "4. Create Session\n";
        cout << "5. Mark Attendance\n";
        cout << "6. View Report\n";
        cout << "0. Exit\n";
        cout << "Choose Option: ";
        cin >> choice;

        switch (choice) {
            case 1: registerStudent(); break;
            case 2: viewStudents(); break;
            case 3: searchStudent(); break;
            case 4: createSession(); break;
            case 5: markAttendance(); break;
            case 6: viewReport(); break;
            case 0: cout << "Exiting Program...\n"; break;
            default: cout << "Invalid Choice!\n";
        }

    } while (choice != 0);

    return 0;
}
