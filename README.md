#include "pch.h"
#include <iostream>
#include<string>
#include<list>
using namespace std;

class AdressInfo
{
protected:
	string City;
	string Street;
	int HouseNum;
	int OfficeNum;
public:
	AdressInfo()
	{
		this->City = "Kharkov";
		this->Street = "Haribaldi";
		this->HouseNum = 5;
		this->OfficeNum = 52;
	}

	AdressInfo(const AdressInfo& obj)
	{
		this->City = obj.City;
		this->Street = obj.Street;
		this->HouseNum = obj.HouseNum;
		this->OfficeNum = obj.OfficeNum;
	}
	~AdressInfo() {}
	string GetSity() { return City; }
	void SetCity(string City) { this->City = City; }

	string GetStreet() { return Street; }
	void SetStreet(string Street) { this->Street = Street; }

	int GetHouseNum() { return HouseNum; }
	void SetHouseNum(int HouseNum) { this->HouseNum = HouseNum; }

	int GetOfficeNum() { return OfficeNum; }
	void SetOfficeNum(int OfficeNum) { this->OfficeNum = OfficeNum; }

	friend ostream& operator<<(ostream& os, const AdressInfo& AdressInfo)
	{
		os << "City : " << AdressInfo.City << "\nStreet : " << AdressInfo.Street << "\nHouse Number : "
			<< AdressInfo.HouseNum << "\nOffice Number : " << AdressInfo.OfficeNum << endl;
		return os;
	};
};

class AnimalEntity
{
public:
	AnimalEntity()
	{
		this->Sex = "men";
		this->Age = "5";
	}

	string GetSex() { return Sex; }
	string GetAge() { return Age; }
	
	friend ostream& operator<<(ostream& os, const AnimalEntity& AnimalEntity)
	{
		os << "Sex: " << AnimalEntity.Sex << "\nAge: " << AnimalEntity.Age << endl;
		return os;
	};
	~AnimalEntity() {}
protected:
	string Sex;
	string Age;
};

class HumanEntity : public AnimalEntity, public AdressInfo
{
public:
	HumanEntity() { "Alex", "Zagoskin"; }
	HumanEntity(string name, string surname)
	{
		this->name = name;
		this->surname = surname;
	}
	void Setname(string name) { this->name = name; }
	string Getname() { return name; }
	void Setsurname(string surname) { this->surname = surname; }
	string Getsurname() { return surname; }



	friend ostream& operator<<(ostream& os, const HumanEntity& HumanEntity)
	{
		os << "Name: " << HumanEntity.name << "\nSurname: " << HumanEntity.surname << endl;
		return os;
	};
	~HumanEntity() {}

protected:
	string name;
	string surname;
};

class Person : public HumanEntity
{
public:
	Person()
	{
		
		PhoneNum = "80992589789";
	}

	Person(string name, string surname, string PhoneNum, string AdressInfo)
	{	
		this->name = name;
		this->surname = surname;
		this->PhoneNum = PhoneNum;
		this->Adress = AdressInfo;
	}
	friend ostream& operator<<(ostream& os, const Person& Person);
	~Person() {}
protected:
	string PhoneNum;
	string Adress;
};

ostream& operator<<(ostream& os, const Person& Person)
{
	os << "Name: " << Person.HumanEntity::name << "\nSurname: " << Person.HumanEntity::surname<< "\nPhone number: " << Person.PhoneNum << "\nSity: " << Person.AdressInfo::City << "\nStreet: " << Person.AdressInfo::Street <<
		"\nHouse Number: " << Person.AdressInfo::HouseNum << "\nOffice Number: " << Person.AdressInfo::OfficeNum << endl;
	return os;
};

struct SalaryCalculator
{
public:
	virtual double calcSalary() = 0;
};

class Oklad : public SalaryCalculator
{
protected:
	double base;
public:
	Oklad() { base = 0; }
	Oklad(double base) { this->base = base; }
	virtual double calcSalary()
	{
		return base;
	}
};

class OkladAward : public SalaryCalculator
{
protected:
	double base;
	double award;
	int days;
public:
	OkladAward() { base = 0; days = 0, award = 0; }
	OkladAward(double base, int days, double award) { this->base = base; this->days = days, this->award = award; }
	virtual double calcSalary()
	{
		return (base / 24) * days + award;
	}
};

class SalaryHour : public SalaryCalculator
{
protected:
	double rate;
	double hour;
public:
	SalaryHour() { hour = 0; rate = 0; }
	SalaryHour(double hour, double rate) { this->hour = hour; this->rate = rate; }
	virtual double calcSalary()
	{
		return hour * rate;
	}
};

class BasePersent : public SalaryCalculator
{
protected:
	double total;
	double percent;
public:
	BasePersent() { total = 0; percent = 0; }
	BasePersent(int total, double percent)
	{
		this->total = total; this->percent = percent;
	}
	virtual double calcSalary()
	{
		return total + total * percent;
	}
};

class Employee : public SalaryCalculator, protected HumanEntity
{
public:
	Employee() 
	{
		position = "no position", Account = "no card account";
	}
	~Employee() {}

	virtual double calcSalary()
	{
		if (this->salaryCalc == NULL)
			return 0;
		else
			return this->salaryCalc->calcSalary();
	}

	
	Employee(string name, string surname,  string position, double base)
		: HumanEntity(name, surname)
	{
		this->position = position;
		this->base = base;
		this->hour = 0;
		this->rate_per_hour = 0;
		this->percent = 0;
		this->total = 0;
		this->salaryCalc = new Oklad(base);
	}

	Employee(string name, string surname, string position, double hour, double rate)
		: HumanEntity(name, surname)
	{
		this->position = position;
		this->base = 0;
		this->hour = hour;
		this->rate_per_hour = rate;
		this->percent = 0;
		this->total = 0;
		this->salaryCalc = new SalaryHour(hour, rate);
	}

	Employee(string name, string surname, string position, double base, int days, double award)
		: HumanEntity(name, surname)
	{
		this->position = position;
		this->days = days;
		this->base = base;
		this->hour = 0;
		this->rate_per_hour = 0;
		this->percent = 0;
		this->total = 0;
		this->award = award;
		this->salaryCalc = new OkladAward(base, days, award);
	}

	Employee(string name, string surname, string position, int total, double percent)
		: HumanEntity(name, surname)
	{
		this->position = position;
		this->days = days;
		this->base = base;
		this->hour = 0;
		this->rate_per_hour = 0;
		this->percent = percent;
		this->total = 0;
		this->award = award;
		this->salaryCalc = new BasePersent(total, percent);
	}

protected:
	string position;
	string Account;
	string Salary;

	int days;
	double base;
	double percent;
	double bonus;
	double hour;
	double rate_per_hour;
	double total;
	double award;
	SalaryCalculator* salaryCalc;
	Employee(const Employee& obj) {}


	friend ostream& operator<< (ostream& os, Employee& data)
	{
		os << "Name : " << data.name << "\nSurname : "<< data.surname<< "\nPosition : " << data.position
			<< "\nSalary = " << data.calcSalary() << endl;
		return os;
	}
};

class Department
{
public:
	Department()
	{
		this->NameDepartment = NameDepartment;
		this->Employees = Employees;
		this->Boss = Boss;
	}
protected:
	string NameDepartment;
	list <string> Employees;
	string Boss;
};

struct CalcInfo
{
	int days;
	double base;
	double percent;
	double bonus;
	double hour;
	double rate_per_hour;
	double total;
	double award;
};

int main()
{

	AdressInfo Adress;
	cout << "Adress\n" << Adress << endl;

	AnimalEntity Animal;
	cout << "Animal\n" << Animal << endl;

	HumanEntity Human;
	Human.Setname("Alex");
	Human.Setsurname("Zagoskin");
	cout << "Human\n" << Human << endl;

	Person person;
	cout << "Person\n" << person << endl;;

	const int N = 8;
	Employee** employes = new Employee*[N];

	employes[0] = new Employee("Ivan", "Petrov", "Janitor", 176, 31.63);
	employes[1] = new Employee("Nikolay", "Sidorov", "Handyman", 180, 34.37);
	employes[2] = new Employee("Olga", "Nikolaevna", "Programmer", 176, 89.91);
	employes[3] = new Employee("Maksim", "Makarov", "Driver", 176, 39.17);
	employes[4] = new Employee("Tatyana", "Beseda", "Accountant", 7512.11, 22, 3000);
	employes[5] = new Employee("Inna", "Borisovna", "Financial analyst", 12512.11, 20, 5000);
	employes[6] = new Employee("Artur", "Victorovich", "Director ", 35000, 0.49);
	employes[7] = new Employee("Anna", "Alekseevna", "Sales Manager ", 17000, 0.195);

	for (int i = 0; i < N; i++)
	{
		cout << *employes[i] << endl;
	}

	for (int i = 0; i < N; i++)
	{
		delete employes[i];
	}
	delete[] employes;
	system("pause");
	return 0;

}

