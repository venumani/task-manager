#include <iostream>
#include <string>

using namespace std;

struct Task {
    int priority;
    string taskID;
    string description;
    Task* next;
};

class TaskManager {
    Task* head;

public:
    TaskManager() : head(nullptr) {}

    void addTask(const string& taskID, int priority, const string& description) {
        Task* newTask = new Task{priority, taskID, description, nullptr};
        if (!head) {
            head = newTask;
        } else {
            Task* temp = head;
            while (temp->next != nullptr) {
                if (temp->taskID == taskID) {
                    cout << "Task ID already exists.\n";
                    delete newTask;
                    return;
                }
                temp = temp->next;
            }
            if (temp->taskID == taskID) {
                cout << "Task ID already exists.\n";
                delete newTask;
                return;
            }
            temp->next = newTask;
        }
        cout << "Task added successfully.\n";
    }

    void getHighestPriorityTask() {
        if (!head) {
            cout << "No pending tasks.\n";
            return;
        }
        Task* temp = head;
        Task* highest = head;
        while (temp != nullptr) {
            if (temp->priority < highest->priority) {
                highest = temp;
            }
            temp = temp->next;
        }
        cout << "TaskID: " << highest->taskID << ", Priority: " << highest->priority << ", Description: " << highest->description << "\n";
    }

    void completeTask(const string& taskID) {
        if (!head) {
            cout << "Task not found.\n";
            return;
        }
        if (head->taskID == taskID) {
            Task* toDelete = head;
            head = head->next;
            delete toDelete;
            cout << "Task completed and removed.\n";
            return;
        }
        Task* temp = head;
        while (temp->next != nullptr && temp->next->taskID != taskID) {
            temp = temp->next;
        }
        if (temp->next == nullptr) {
            cout << "Task not found.\n";
            return;
        }
        Task* toDelete = temp->next;
        temp->next = temp->next->next;
        delete toDelete;
        cout << "Task completed and removed.\n";
    }

    void listTasks() {
        if (!head) {
            cout << "No pending tasks.\n";
            return;
        }
        // Since linked list is unsorted, we can copy to array and sort or just print as is
        // For simplicity, print unsorted
        Task* temp = head;
        while (temp != nullptr) {
            cout << "TaskID: " << temp->taskID << ", Priority: " << temp->priority << ", Description: " << temp->description << "\n";
            temp = temp->next;
        }
    }

    ~TaskManager() {
        while (head) {
            Task* temp = head;
            head = head->next;
            delete temp;
        }
    }
};

int main() {
    TaskManager tm;
    string line;

    cout << "Welcome to Task Manager!\n";

    while (true) {
        cout << "> ";
        getline(cin, line);
        if (line == "exit") break;

        if (line.substr(0, 3) == "add") {
            size_t pos1 = line.find(' ', 4);
            size_t pos2 = line.find(' ', pos1 + 1);
            if (pos1 == string::npos || pos2 == string::npos) {
                cout << "Invalid add command format.\n";
                continue;
            }
            string taskID = line.substr(4, pos1 - 4);
            int priority = stoi(line.substr(pos1 + 1, pos2 - pos1 - 1));
            string description = line.substr(pos2 + 1);
            tm.addTask(taskID, priority, description);
        } else if (line == "get") {
            tm.getHighestPriorityTask();
        } else if (line.substr(0, 8) == "complete") {
            string taskID = line.substr(9);
            tm.completeTask(taskID);
        } else if (line == "list") {
            tm.listTasks();
        } else {
            cout << "Unknown command.\n";
        }
    }

    cout << "Exiting Task Manager.\n";
    return 0;
}