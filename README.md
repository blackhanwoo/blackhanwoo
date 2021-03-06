#include <iostream>
#include <cstring>
#include <string>

using namespace std;
const int NAME_LEN = 20;

enum{MAKE=1,DEPOSIT,WITHDRAW,INQUIRE,EXIT};

// Accounthandler 라는 컨트롤클래스를 정의하고, 전역함수들을 이클래스의 맴버함수에 포함
// Account 객체의 저장을 위해 선언한 배열과 변수도 이 클래스의 맴버에 포함
// AccountHandler 클래스를 기반으로 프로그램이 실행되도록 main 함수를 변경.

class Account
{
private:
	int accID; // 계좌번호
	int balance; // 잔액
	char* cusName; // 고객이름

public:
	Account(int ID, int money, char* name);
	Account(const Account& ref);

	int GetAccID() const;
	void Deposit(int money);
	int withdraw(int money);
	void ShowAccInfo()const;
	~Account();

};
Account::Account(int ID, int money, char* name)
	:accID(ID), balance(money)
{
	cusName = new char[strlen(name) + 1];
	strcpy_s(cusName, strlen(name) + 1, name);
}

Account::Account(const Account& ref)
	:accID(ref.accID), balance(ref.balance)
{
	cusName = new char[strlen(ref.cusName) + 1];
	strcpy_s(cusName, strlen(ref.cusName) + 1, ref.cusName);
}
int Account::GetAccID() const { return accID; }

void Account::Deposit(int money)
{
	balance += money;
}

int Account::withdraw(int money) // 출금액 반환, 부족시 0 반환
{
	if (balance < money)
	{
		return 0;
	}
	balance -= money;
	return money;
}

void Account::ShowAccInfo()const
{
	cout << "계좌ID:" << accID << endl;
	cout << "이 름:" << cusName << endl;
	cout << "잔 액:" << balance << endl;
}

Account::~Account()
{
	delete[]cusName;
}

class AccountHandler
{
private:
	Account* accArr[100];
	int accNum;
public:
	AccountHandler();
	void showMenu(void) const;
	void MakeAccount(void);
	void Depositmoney(void);
	void withdrawmoney(void);
	void showallaccinfo(void) const;
	~AccountHandler();
};

void AccountHandler::showMenu(void) const
{
	cout << "-----Menu-----" << endl;
	cout << "1.계좌개설" << endl;
	cout << "2.입 금" << endl;
	cout << "3.출 금" << endl;
	cout << "4. 계좌정보 전체 출력" << endl;
	cout << "5. 프로그램 종료" << endl;
}

void AccountHandler::MakeAccount(void) {
	int id;
	char name[NAME_LEN];
	int balance;

	cout << "[계좌개설]" << endl;
	cout << "계좌ID:"; cin >> id;
	cout << "이 름:"; cin >> name;
	cout << "입금액:"; cin >> balance;
	cout << endl;

	accArr[accNum++] = new Account(id, balance, name);
}

void AccountHandler::Depositmoney(void) {
	int money;
	int id;

	cout << "[입금]" << endl;
	cout << "계좌ID:"; cin >> id;
	cout << "입금액 :"; cin >> money;

	for (int i = 0; i < accNum; i++)
	{
		if (accArr[i]->GetAccID() == id)
		{
			accArr[i]->Deposit(money);
			cout << "입금완료" << endl << endl;
			return;
		}
	}
	cout << "유효하지 않는 ID입니다." << endl << endl;
}

void AccountHandler::withdrawmoney(void) {
	int money;
	int id;
	cout << "[출금]" << endl;
	cout << "계좌ID: "; cin >> id;
	cout << "출금액:"; cin >> money;

	for (int i = 0; i < accNum; i++)
	{
		if (accArr[i]->GetAccID() == id)
		{
			if (accArr[i]->withdraw(money) == 0)
			{
				cout << "잔액부족" << endl << endl;
				return;
			}
			cout << "출금완료" << endl << endl;
			return;
		}
	}
	cout << "유효하지 않는 ID입니다." << endl << endl;
}
AccountHandler::AccountHandler():accNum(0)
{}

void AccountHandler::showallaccinfo(void) const {
	for (int i = 0; i < accNum; i++)
	{
		accArr[i]->ShowAccInfo();
		cout << endl;
	}
}

AccountHandler::~AccountHandler()
{
	for (int i = 0; i < accNum; i++)
		delete accArr[i];
}

int main(void)
{
	AccountHandler manager;
	int choice;

	while (1)
	{
		manager.showMenu();
		cout << "선택 : ";
		cin >> choice;
		cout << endl;

		switch (choice)
		{
		case MAKE:
			manager.MakeAccount();
			break;
		case DEPOSIT:
			manager.Depositmoney();
			break;
		case WITHDRAW:
			manager.withdrawmoney();
			break;
		case INQUIRE:
			manager.showallaccinfo();
			break;
		case EXIT:

			return 0;
		default:
			cout << "illegal selection.." << endl;
		
		}
	}
	return 0;
} 
