#include <iostream>																					
using namespace std;

// the data values and the filter
struct TheFilter {
	double* Values;   // the filter values																//Pointer to a double float called Values, i.e. filter values
	unsigned long Length;  // number of filter values													//Declares long positive integer value, i.e. length of array
	bool Valid;   // true if the filter values have been obtained										//Later use if Valid = true, continue, cout filter values obtained
};

struct TheData {
	double* Values;  // holds the data to be filtered
	unsigned long Length;  // number of data values
	bool Valid;   // true if the data values have been obtained
};

// function return values
enum { OK, FILTER_TOO_LONG };

// function prototypes
void EnterData(TheData* OriginalData);
void EnterFilter(TheFilter* Filter);
int ApplyFilter(TheData* OriginalData, TheData* FilteredData, TheFilter* Filter);
void DisplayData(TheData* OriginalData, TheData* FilteredData, TheFilter* Filter);

int main()
{
	// define the filter and its initial values
	TheFilter Filter = { 0,0,false };

	// define the original data and its initial values
	TheData OriginalData = { 0,0,false };

	// define the filtered data and its initial values
	TheData FilteredData = { 0,0,false };

	char UserInput;

	// loop until the user wishes to exit
	while (1) {

		// show the menu of options
		cout << endl;
		cout << "Filter Menu" << endl;
		cout << "-----------" << endl;
		cout << "1. Enter data for filtering" << endl;
		cout << "2. Enter filter values" << endl;
		cout << "3. Apply filter" << endl;
		cout << "4. Display filtered data" << endl;
		cout << "5. Exit from the program" << endl << endl;

		// get the user's choice
		cout << "Enter your option: ";
		cin >> UserInput;
		cout << endl;

		// act on the user's input
		switch (UserInput) {																			//Switch statement allows a variable to be tested for equality against a list of values.
		case '1':																						//Each value is called a case, and the variable being switch on is checked for each case.
//			cout << "Commencing EnterData Function." << endl;
			EnterData(&OriginalData);
			FilteredData.Valid = false;
			break;

		case '2':
//			cout << "Commencing EnterFilter Function" << endl;
			EnterFilter(&Filter);
			FilteredData.Valid = false;
			break;

		case '3':
//			cout << Filter.Length;
//			cout << "Commencing if statement to check Filter and Original Data are true, and Filtered Data is false." << endl;
//			cout << Filter.Valid << endl << OriginalData.Valid << endl << FilteredData.Valid << endl;
			if (Filter.Valid == true && OriginalData.Valid == true && FilteredData.Valid == false) {
//				cout << "Commencing if statement to check if Filter Length < Original Data Length." << endl;
				if (ApplyFilter(&OriginalData, &FilteredData, &Filter) == FILTER_TOO_LONG) {
					cout << "The filter must not be longer than the data" << endl;
				}
				else {
					FilteredData.Valid = true;
					cout << "Filter applied" << endl;
				}
			}
			break;

		case '4':
//			cout << "Commence if statement to check whether Filter, Original, and Filtered are true." << endl;
//			cout << Filter.Valid << endl << OriginalData.Valid << endl << FilteredData.Valid << endl;
			if (Filter.Valid == true && OriginalData.Valid == true && FilteredData.Valid == true) {
				DisplayData(&OriginalData, &FilteredData, &Filter);
			}
			else {
				cout << "Data have not yet been filtered" << endl;
			}
			break;

		case '5':
			delete[] Filter.Values;
			delete[] OriginalData.Values;
			delete[] FilteredData.Values;
			return 0;
			break;

		default:
			cout << "Invalid entry" << endl << endl;
			break;
		}
	}
}

void EnterData(TheData* OriginalData)
{
	// initialize the data structure
	cout << endl << "How many data values would you like to enter? ";
	cin >> OriginalData->Length;

	// allocate memory to the data
	OriginalData->Values = new double[OriginalData->Length];
	if (OriginalData->Values == 0) {
		cerr << "\n*** Unable to allocate memory **\n";
		return;
	}
	else {
		cout << "Please input the data values one at a time:" << endl;
		for (unsigned int i = 0; i < OriginalData->Length; i++) {
			cin >> OriginalData->Values[i];
		}
		cout << endl;
		OriginalData->Valid = true;
	}

//	cout << OriginalData->Valid;
}

void EnterFilter(TheFilter* Filter)
{
	// initialize the data structure
	cout << endl << "How many filter values would you like to enter? ";
	cin >> Filter->Length;

	// allocate memory to the filter values
	Filter->Values = new double[Filter->Length];
	if (Filter->Values == 0) {
		cerr << "\n*** Unable to allocate memory **\n";
		return;
	}
	else {
		cout << "Please input the filter values one at a time:" << endl;
		for (unsigned int i = 0; i < Filter->Length; i++) {
			cin >> Filter->Values[i];
		}
		cout << endl;
		Filter->Valid = true;
	}
//	cout << Filter->Valid;
}
 
int ApplyFilter(TheData* OriginalData, TheData* FilteredData, TheFilter* Filter)
{
	// return an error if the filter is longer than the data
	if (Filter->Length > OriginalData->Length) return FILTER_TOO_LONG;
	
	// initialize the data structure that holds the filtered data
	FilteredData->Length = (OriginalData->Length - Filter->Length) + 1;

	// get memory for the filtered data
	FilteredData->Values = new double[FilteredData->Length];

	if (FilteredData->Values == 0) {
		cerr << "\n*** Unable to allocate memory **\n";
		return 0;
	}
	else {
		for (unsigned int i = 0; i < FilteredData->Length; i++) {
			FilteredData->Values[i] = 0;
			int filter_index = 0;
			for (unsigned int j = i; j < i + Filter->Length; j++) {
				FilteredData->Values[i] += OriginalData->Values[j] * Filter->Values[filter_index];
				filter_index++;
			}
		}
		FilteredData->Valid = true;
	}
	return OK;
}

void DisplayData(TheData* OriginalData, TheData* FilteredData, TheFilter* Filter)
{
	cout << "The filtered data is ";
	for (unsigned int i = 0; i < FilteredData->Length; i++) {
		cout << FilteredData->Values[i] << " ";
	}
	cout << endl;
}
