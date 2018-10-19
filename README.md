# MAC125-LinkedList-Project
The Final Project I created for my Advanced C++ Class

// Kyle Wynne - MAC 125 - Linked List Project
// Created functions to add records to beginning and end, as well as insert.
// Created functions to populate, display, count and check
// Created function to modify existing records telephone number
// Added exception handling when the user is at the menu, previously if user entered a string instead of integer the program would bug out
// now it returns an error message and terminates.

#include <iostream>
#include <string>
#include <iomanip>
#include <stdlib.h>
#include <algorithm>
#include <stdexcept>
using namespace std;

struct Teletype
{
	string name;
	string phonenum;
	struct Teletype* next;
};

void populate(Teletype *); // Populate record
void display(Teletype *); // Display records
void remove(Teletype **); // Remove a record
void beginning(Teletype**); // Add a record to the beginning of linkedlist
void end(Teletype**); // Add a record to the end of linkedlist
void insert(Teletype**); // Insert a record behind another
int find(Teletype *, string); // Search for a record in the linkedlist
void modify(Teletype **); // Modify an existing records phone number
int check(Teletype *); // Function to check if storage is available to be allocated.
int count(Teletype *); // Counts and returns total number of records

int main()
{
	char key;
	string opt, new_name, new_num, name;
	int total = 0, choice;
	Teletype *list, *current, *first;
	list = new Teletype;
	current = list;

	cout << "Would you like to create a new record? (y/n): " << endl;
	key = cin.get();

	while (key != 'n')
	{
		key = cin.get();
		check(current); // new Teletype will create NULL if no space is available, function checks for NULL;
		populate(current);
		cout << "Would you like to create another record? (y/n): " << endl;
		key = cin.get();
		if (key != 'n')
		{
			current->next = new Teletype;
			current = current->next;
		}
		total++;
	}

	current->next = NULL;
	first = list;
	display(list);
	int pick;
	try
	{
	bool operation = true;
	while (operation != false)
	{
		cout << "Total records entered: " << count(first) << endl;
		cout << "--------------------" << endl;
		cout << "1 - Delete a record" << endl;
		cout << "2 - Insert a record" << endl;
		cout << "3 - Modify a record" << endl;
		cout << "4 - Find a record" << endl;
		cout << "5 - Quit Program" << endl;
		cout << "--------------------" << endl;
		cout << "Choose an action: " << endl;
		cin >> choice;
		if (cin.fail()) throw runtime_error("Input is not an integer.\n"); // Throw runtime error instead of crashing program

		switch (choice)
		{
		case 1:
			cout << "\nChoose a record to delete: " << endl;
			remove(&first);
			display(first);
			break;
		case 2:
			cout << "\nWhere would you like to add new record? (1 - Beginning, 2 - End, 3 - Middle): " << endl;
			cin >> pick;
			if (cin.fail()) throw runtime_error("Input is not an integer.\n"); // Throw runtime error instead of crashing program
			switch (pick)
			{
			case 1:
				beginning(&first);
				display(first);
				break;
			case 2:
				end(&first);
				display(first);
				break;
			case 3:
				insert(&first);
				display(first);
				break;
			default:
			    cout << "Not a valid choice. Please pick again (1-3): " << endl;
			    break;
			}
			break;
		case 3:
			cout << "\nChoose a record to modify: " << endl;
			modify(&first);
			display(first);
			break;
		case 4:
			cin.ignore();
			cout << "\nSearch for a record (First Last): " << endl;
			getline(cin, name);
			if (!find(first, name))
				cout << "Name is not in the current directory." << endl;
			break;
		case 5:
			cout << "\nTerminating Program." << endl;
			operation = false;
			break;
		default:
			cout << "Not a valid choice. Please pick again (1-5)." << endl;
			display(first);
			break;
		}
	}
}
catch (const runtime_error& e)
{
    cout << "Error: " << e.what() << endl;
    cout << "Terminating Program." << endl;
	system("pause");
	return 0;
}
    system("pause");
    return 0;
}

// Implementation

void remove(struct Teletype **head) // Remove a record by name
{
	string del;
	cin.ignore();
	cout << "Insert name of record to remove: " << endl;
	getline(cin, del);
	struct Teletype* temp = *head, *prev;

	if (temp != NULL && temp->name == del)
	{
		*head = temp->next;
		free(temp);
		return;
	}
	while (temp != NULL && temp->name != del)
	{
		prev = temp;
		temp = temp->next;
		if (temp->name == del)
		{
			prev->next = temp->next;
			free(temp);
			break;
		}
		else if (temp == NULL) cout << "Record not found. " << endl;
	}
}

void beginning(struct Teletype **head) // Add a record to the beginning of linked list
{
	string new_name, new_num;
	cin.ignore();
	cout << "Adding record to the beginning of list: " << endl;
	cout << "What is the name of the new record?: " << endl;
	getline(cin, new_name);
	cout << "What is the number of the new record?: " << endl;
	getline(cin, new_num);

	struct Teletype* new_node = new Teletype;

	new_node->name = new_name;
	new_node->phonenum = new_num;

	new_node->next = *head;

	*head = new_node;
}

void end(struct Teletype** head) // Add a structure to end of linked list
{
	string new_name, new_num;
	cin.ignore();
	cout << "Adding record to end of linked list: " << endl;
	cout << "What is the name of the new record?: " << endl;
	getline(cin, new_name);
	cout << "What is the number of the new record?: " << endl;
	getline(cin, new_num);
	/* 1. allocate node */
	struct Teletype* new_node = new Teletype;

	struct Teletype *last = *head;

	new_node->name = new_name;
	new_node->phonenum = new_num;

	new_node->next = NULL;

	if (*head == NULL)
	{
		*head = new_node;
		return;
	}

	while (last->next != NULL)
		last = last->next;

	last->next = new_node;
	return;
}


void populate(Teletype *record) // Create a record
{
	cout << "Enter a name: ";
	getline(cin, record->name);
	cout << "Enter a telephone number: ";
	getline(cin, record->phonenum);
	return;
}

void display(Teletype *contents) // Display the records
{
    cout << "Current records: " << endl;
	while (contents != NULL)
	{
		cout << endl << left
			<< setw(30) << contents->name
			<< setw(20) << contents->phonenum << endl;
		contents = contents->next;
		cout << endl;
	}
}

int find(Teletype *contents, string name) // Locate a name and return the record
{
	int found = 0;

	while (contents != NULL)
	{
		string test = contents->name;
		//transform(name.begin(), name.end(), name.begin(), toupper);
		//transform(test.begin(), test.end(), test.begin(), toupper); // Search does not need to be CASE sensitive to capitals.
		if (test == name)
		{
			found = 1;
			cout << endl;
			cout << left << setw(30) << contents->name << setw(20) << contents->phonenum << endl;
			cout << endl;
			break;
		}
		else
		{
			contents = contents->next;
		}
	}
	return found;
}
void insert(struct Teletype** head) // Insert a record behind another
{
	string name, new_name, new_num;
	cin.ignore();
	cout << "Enter the name of the record you would like to insert after: " << endl;
	getline(cin, name);
	struct Teletype* temp = *head;
	struct Teletype* new_node = new Teletype;

    while (temp != NULL)
    {
	    if (temp->name == name)
    	{
    	    cout << "What is the name of the new record? " << endl;
	        getline(cin, new_name);
	        cout << "what is the number of the new record? " << endl;
	        getline(cin, new_num);
	    	new_node->name = new_name;
	    	new_node->phonenum = new_num;
	    	new_node->next = temp->next;
	    	temp->next = new_node;
	    	break;
	    }
	    else
	    {
	    	temp = temp->next;
	    }
    }
    if (temp == NULL)
    cout << "Record was not found in list." << endl;
}

void modify(struct Teletype** head) // Modify an existing records phone number
{
	string name, new_num;
	cin.ignore();
	cout << "Insert the name of the record you would like to modify: " << endl;
	getline(cin, name);

	struct Teletype* temp = *head;
	while (temp != NULL)
	{
		if (temp->name == name)
		{
			cout << "Insert the new phone number:  " << endl;
			getline(cin, new_num);
			temp->phonenum = new_num;
			break;
		}
		else
		{
			temp = temp->next;
		}
	}
	if (temp == NULL)
	{
		cout << "\nRecord is not in the current list." << endl;
	}
}
int count(struct Teletype* contents) // Count and return the amount of total records
{
    int count = 0;
    while (contents != NULL)
    {
        contents = contents->next;
        count++;
    }
    return count;
}

int check(Teletype* contents)
{
    if (contents == NULL) // new Teletype will create NULL if no space is available
    {
        cout << "No additional memory available. List at capacity." << endl;
        return 1;
    }
    else 
    return 0;
}
