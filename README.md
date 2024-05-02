# Unit5MeunAssignmentRodolfoSaucedo
//Rodolfo Saucedo 5/1/2024 Dr.T assignment 5 
/*Ai helped me quite a bit so want to give credit to it. Helped me write the code for the receicpt and also helped me place the really badly designed tip code and cash/card accepter code. I know that reciept code doesn't not work completely since it doesnt correctly keep track of totals and change most in part to the poorly designed tip and money accepter code. Regardless I do like how it came out regardless. */


#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <iomanip>
#include <stdlib.h>
#include <fstream>
using namespace std;
#define RESET   "\033[0m"
#define GREEN   "\033[32m"      
#define RED     "\033[31m"
#define BLACK   "\033[30m"
#define BLUE    "\033[34m"



class MenuItem{
private:
string name;
double price;
char addLetter, removeLetter;
int count;  
string desc;

public:
void setName(string n){name = n;}
void setPrice(double p){price = p;}
void setAddLetter(char aL){addLetter = aL;}
void setRemoveLetter(char rL){removeLetter = rL;}
void setDesc(string d){desc = d;}
void setCount(int c){count = c;}
string getName()const {return name;}
double getPrice()const {return price;}
char getAddLetter()const {return addLetter;}
char getRemoveLetter()const {return removeLetter;}
string getDesc()const {return desc;}
int getCount()const {return count;}

};
double AcceptTip(double &subtotal);
void printReceipt(vector<MenuItem> &m, double &subtotal, double &tax, double &tip, double &total, double &change, double &cash);

void populateMenu(vector<MenuItem> &entireMenu)
{
  //put some default values in our Menu
  const int numItems = 7; 
  MenuItem Item1; 
  MenuItem Item2;
  MenuItem Item3; 
  MenuItem Item4;
  MenuItem Item5;
  MenuItem Item6;
  MenuItem Item7;    

  entireMenu.push_back(Item1); //add to the end of list the Item1
  entireMenu.push_back(Item2); //add to the end of list the Item2
  entireMenu.push_back(Item3); //add to the end of list the Item3
  entireMenu.push_back(Item4); //add to the end of list the Item4
  entireMenu.push_back(Item5); //add to the end of list the Item5
  entireMenu.push_back(Item6); //add to the end of list the Item6
  entireMenu.push_back(Item7); //add to the end of list the Item7

  vector<string> defaultMenuNames = {"Green Tea", "Burrito", "Brisket", "Burger", "Lemonade", "Steak", "PizzaSlice"}; 
  vector<char> defaultAddLetters = {'A', 'B', 'C', 'D', 'E', 'F', 'G'}; 
  vector<char> defaultRemoveLetters = {'a', 'b', 'c', 'd', 'e', 'f', 'g'}; 

  for(int i = 0; i < numItems; i++)
  {
    //add each item to the default list efficiently 
    entireMenu[i].setName(defaultMenuNames[i]); 
    entireMenu[i].setAddLetter(defaultAddLetters[i]); 
    entireMenu[i].setRemoveLetter(defaultRemoveLetters[i]); 
    entireMenu[i].setPrice(3.00 + i); //set a random starter cost for each item
    entireMenu[i].setCount(0); //initialze all counts to 0
    entireMenu[i].setDesc("Tasty"); //set all default desc to "delicous"
  }


}

void showMenu(vector<MenuItem> &m)
{
  cout << fixed << setprecision(2);//set doubles to 2 decimal places
  cout << "DrT's Updated Effcient Menu" << endl; 
  cout << "ADD  \tNAME \t COST \tREMOVE\tCOUNT\tDESC"<<endl; 
  for(int i = 0; i < m.size(); i++)
  {
    cout << m[i].getAddLetter() << ")" << setw(10) << m[i].getName() 
    << setw(5) << "$" << m[i].getPrice() << setw(5) << "(" << m[i].getRemoveLetter()
    << ")" << setw(7) << m[i].getCount() << setw(13) << m[i].getDesc() 
    <<endl; 
  }

}

void acceptOrder(vector<MenuItem> &m)
{
  char option = '\0';// the user-selected menu item
  double subtotal = 0.0; 
  double Customtip = 0.0;
  double taxes = 0.0;
  double total = 0.0;
  double change = 0.0;
  double cash = 0.0;
  do
  {
    cout << "\nPlease choose an item (x to Exit)(Enter P to pay): ";
    cin >> option; 

    for(int i=0; i < m.size(); i++)
    {
      if(option == m[i].getAddLetter())
      {
        cout << "\nMenu Item " << m[i].getAddLetter() << " selected."; 
        m[i].setCount(m[i].getCount() +1); //increment the count by 1
        cout << "\033[2J\033[1;1H"; //clear screen 
        cout << "\n1 UP on " << m[i].getName() << endl;
        subtotal += m[i].getPrice(); //increment the subtotal by the cost of the item 
        showMenu(m); //show the updated menu
        cout << "\nSubtotal: $" << subtotal << endl;  
      }
      else if(option == m[i].getRemoveLetter())
      {
        cout << "\nRemove Item " << m[i].getRemoveLetter() << " selected."; 
        if(m[i].getCount() > 0) //subtract if and only if the count is > 0
        {
          m[i].setCount(m[i].getCount()-1); //decrement the count by 1
          cout << "\033[2J\033[1;1H"; //clear screen 
          cout << "\n1 DOWN on " << m[i].getName() << endl;
          subtotal -= m[i].getPrice(); //decrement the subtotal by the cost of the item
          showMenu(m); //show the updated menu
          cout << "\nSubtotal: $" << subtotal << endl;  
        }
        else //the the user why you blocked item removal 
        {

            cout << "\nItem count must be > 0 to remove: " << m[i].getName() << endl;
        }
      }
      else if(
                option != m[i].getAddLetter() && 
                option != m[i].getRemoveLetter() &&
                option != 'x' &&
                option != 'X' &&
                option != 'p' &&
                option != 'P'
            ) //test for all of my valid inputs
            {
              if(i == 0)
              {
                cout << "\nInvalid input (" << option << "). Please try again." << endl; 
              }  
            }
      else if (option == 'P' || option == 'p'){
        //AcceptTip(subtotal);
       
        double recomendedTip = subtotal * 0.20;
        double Customtip = 0.0;
        double taxes = subtotal * 0.0825;
        double total = subtotal + taxes;
        char choice = '\0';
        cout <<BLUE<< "Would you like to add a tip? (Y/N): "<< RESET;
        cin >> choice;
        if (choice == 'y' || choice == 'Y'){
          cout <<"Suggested amounts 20 % ("<< recomendedTip <<"):"<< endl;
          cout << "Enter custom amount: ";
          cin >> Customtip;
          total = subtotal + taxes + Customtip;
          cout << "Your Total is: $" << total << endl;
        }
        else if (choice == 'n' || choice == 'N'){
          cout << "Your Total is: $" << total << endl;
        }
        
        cout <<BLACK <<"Cash or Credit Card? Enter 1 for Cash or 2 for Credit Card: "<<RESET;
        cin >> choice; 
        double cash = 0.0;
        
        if (choice == '1'){
          do{
          cout << "Enter Cash Amount: ";
          cin >> cash;
          if (cash < total){
            cout <<RED<< "Insufficient Funds" << endl;
            cout << "Food will only be served to those who pay in full (You cheapskate!). Have a nice day!" <<RESET<< endl;
            exit(0);
          }
          else if(cash == total){
            cout <<GREEN<< "You paid the exact amount!\nThank you for your purchase!" << endl;
          }
          else if (cash > total){
            double change = cash - total;
            cout << GREEN<<"Your change is: $" << change <<RESET << endl;
            break;
          }
          }while(total < cash);
            break;
        }
        else if (choice == '2'){
          cout <<BLUE<< "Thank you for your purchase!" << RESET<<endl;
        }
          break;
      }
    }
    //failed attempt here at tipping
   /* double recomendedTip = subtotal * 0.20;
    double Customtip = 0.0;
    double taxes = subtotal * 0.0825;
    double total = subtotal + taxes;
    char choice = '\0';
    cout << "Would you like to add a tip? (Y/N): ";
    if (choice == 'y' || choice == 'Y'){
      cout <<"Suggested amounts 20 % ("<< recomendedTip <<"):"<< endl;
      cout << "Enter custom amount: ";
      cin >> Customtip;
      total = subtotal + taxes + Customtip;
      cout << "Your Total is: $" << total << endl;
    }
    else if (choice == 'n' || choice == 'N'){
      cout << "Your Total is: $" << total << endl;
    }*/

  } while (option != 'x' && option != 'X' && option != 'P' && option != 'p'); 
  cout << "\nThank you for placing your order." << endl; 
 printReceipt(m, subtotal, taxes, Customtip, total, change, cash);
  //another failed attempt at tipping, though this was the first attempt i made 
 /* double recomendedTip = subtotal * 0.20;
  double Customtip = 0.0;
  double taxes = subtotal * 0.0825;
  double total = subtotal + taxes;
  char choice = '\0';
 
  do{
    cout << "Would you like to add a tip? (Y/N): ";
  if (choice == 'y' || choice == 'Y'){
    cout <<"Suggested amounts 20 % ("<< recomendedTip <<"):"<< endl;
    cout << "Enter custom amount: ";
    cin >> Customtip;
    total = subtotal + taxes + Customtip;
    cout << "Your Total is: $" << total << endl;
  }
    
  }while (choice == 'N' || choice == 'n');*/
  
}
void printReceipt(vector<MenuItem> &m, double &subtotal, double &tax, double &tip, double &total, double &change, double &cash){
  cout << BLUE <<"Here is your receipt" << RESET<<endl;
ofstream receipt("receipt.txt");
  receipt << "DrT's Effcient Receipt" << endl;
  receipt << "-----------------------" << endl;
  for(int i = 0; i < m.size(); i++){
    if(m[i].getCount() > 0){
      receipt << m[i].getName() << "\t" << m[i].getCount() << "\t" <<
        endl;
      
    }
  }
  receipt << "-----------------------" << endl;
  receipt << "Subtotal: $" << subtotal << endl;
  receipt << "Tax: $" << tax << endl;
  receipt << "Tip: $" << tip << endl;
  receipt << "Total: $" << total << endl;
  receipt << "You paid: $" << cash << endl;
  receipt << "Change: $" << change << endl;
  //credit to AI here 
        receipt.close();
    ifstream readRec("receipt.txt");
  string line;
  if(readRec.is_open()){
    while( getline(readRec, line)){
      cout << line << endl;
    }
 readRec.close(); 
  }
  else {
    cout << "Unable to open file" << endl;
  }
  
}



int main() {
  vector<MenuItem> wholeMenu;
  populateMenu(wholeMenu);
  showMenu(wholeMenu);
  acceptOrder(wholeMenu);
 



return 0;  
}

//ignore this function was just going to be called but ended up not doing so
double AcceptTip(double &subtotal){
double recomendedTip = subtotal * 0.20;
double Customtip = 0.0;
double taxes = subtotal * 0.0825;
double total = subtotal + taxes;
char choice = '\0';
cout << "Would you like to add a tip? (Y/N): ";
  cin >> choice;
if (choice == 'y' || choice == 'Y'){
  cout <<"Suggested amounts 20 % ("<< recomendedTip <<"):"<< endl;
  cout << "Enter custom amount: ";
  cin >> Customtip;
  total = subtotal + taxes + Customtip;
  cout << "Your Total is: $" << total << endl;
}
else if (choice == 'n' || choice == 'N'){
  cout << "Your Total is: $" << total << endl;
}
return subtotal;}
