#include <iostream>
#include <string>
using namespace std;

class Topic{
public:
	string tname;
	int importance;							//must be between 1 and 10
	string subject;
	Topic(){ tname = " ", importance = 1, subject = " "; }
	Topic(string tn, int imp, string sub){
		tname = tn;
		if (imp<10 && imp>1)
			importance = imp;
		else if (imp<1 || imp>10)
			importance = 1;
		subject = sub;
	}
};
class TopicNode{ 		//linkedlist	3
public:
	Topic topic;
	TopicNode *tnext;
	TopicNode(){ tnext = NULL; }
	TopicNode(Topic tp){ topic = tp, tnext = NULL; }
};
class TopicList{
private:
	TopicNode *topichead;
public:
	TopicList(){ topichead = NULL; }
	void addtolist(Topic topic);
	bool searchfortp(TopicNode *tpnode);
	void printlist();
};

class Student{
public:
	string stname;
	int id;
	int age;
	string gender;
	TopicNode *thead;
	Student(){
		stname = " ";
		id = -1;
		age = -1;
		gender = " ";
		thead = NULL;
	}
	Student(string stn, int i, int a, string g){
		stname = stn;
		id = i;
		age = a;
		gender = g;
		thead = NULL;
	}
};
class StudentNode{		//tree			2
public:
	Student student;
	StudentNode *right;
	StudentNode *left;
	StudentNode(){ right = left = NULL; }
	StudentNode(Student s){ student = s, right = left = NULL; }
};

class Semester{
public:
	string sname;
	int period;
	int nos;
	StudentNode *root;
	Semester(){
		sname = " ";
		period = 0;
		nos = 0;
		root = NULL;
	}
	Semester(string sn, int p, int ns){
		sname = sn;
		period = p;
		nos = ns;
		root = NULL;
	}
};
class SemesterNode{		//linkedlist	1
public:
	Semester semester;
	SemesterNode *snext;
	SemesterNode(){ snext = NULL; }
	SemesterNode(Semester sem){ semester = sem, snext = NULL; }
};

class Database {
private:
	SemesterNode *shead;
public:
	Database(){ shead = NULL; }
	void addsemester(Semester semnode);
	bool addstudent(string semname, string stuname, int i, int a, string g);
	//	1
	bool addtopic(string semname, string n, Topic tp);
	//	2
	void seetree(StudentNode *stnode, int imp);
	void showtopics(int imp);
	//	3
	bool findstu(string stuname, SemesterNode *semnode);
	StudentNode *findstud(string stuname, SemesterNode *semnode);
	void thirdfunct(string stuname, TopicList &tplist, Database &datab);

	//	4
	void searchtree(StudentNode *stnode, int ag);
	void showstudents(string n, int ag);
	//	5
	void del(StudentNode *&q);
	void delstudent(string n);
};

void main(){
	Database d;
	Semester s1("20201", 90, 350);
	Semester s2("20202", 92, 400);

	d.addsemester(s1);
	d.addsemester(s2);

	cout << d.addstudent("20201", "ahmed", 2019400, 19, "male") << endl;
	cout << d.addstudent("20201", "rana", 2018400, 20, "female") << endl;
	cout << d.addstudent("20201", "omar", 2017400, 21, "male") << endl;
	cout << d.addstudent("20202", "hind", 2019500, 19, "female") << endl;
	cout << d.addstudent("20202", "hani", 2018500, 20, "male") << endl;
	cout << d.addstudent("20202", "ahmed", 2019400, 19, "male") << endl;

	Topic t1("linked list", 8, "ADS");
	Topic t2("random variable", 4, "PS");
	Topic t3("integrated circuits", 5, "LC");
	Topic t4("MIPS proccessor", 4, "CO");
	Topic t5("pointers", 9, "PR1");
	Topic t6("queue", 3, "ADS");
	Topic t7("probability", 7, "ps");

	cout << d.addtopic("20201", "ahmed", t1) << endl;
	cout << d.addtopic("20201", "ahmed", t2) << endl;
	cout << d.addtopic("20201", "omar", t2) << endl;
	cout << d.addtopic("20201", "omar", t3) << endl;
	cout << d.addtopic("20201", "rana", t2) << endl;
	cout << d.addtopic("20201", "rana", t1) << endl;
	cout << d.addtopic("20202", "hind", t4) << endl;
	cout << d.addtopic("20202", "hind", t5) << endl;
	cout << d.addtopic("20202", "hani", t4) << endl;
	cout << d.addtopic("20202", "hani", t2) << endl;
	cout << d.addtopic("20202", "ahmed", t6) << endl;
	cout << d.addtopic("20202", "ahmed", t1) << endl;

	d.showtopics(3);
	cout << endl;
	d.showstudents("20201", 17);
	d.showstudents("20202", 17);
	d.delstudent("ahmed");
	cout << endl;
	d.showstudents("20201", 17);
	d.showstudents("20202", 17);
	cout << endl;

	system("pause");
}

void Database::addsemester(Semester sem){
	SemesterNode *semnode = new SemesterNode(sem);
	semnode->snext = shead;
	shead = semnode;
}
bool Database::addstudent(string semname, string stuname, int i, int a, string g){
	Student stu(stuname, i, a, g);
	SemesterNode *semnode = shead;
	while (semnode != NULL){
		if (semnode->semester.sname == semname){
			if (semnode->semester.root == NULL){
				semnode->semester.root = new StudentNode(stu);
				return true;
			}
			else{
				StudentNode* p = semnode->semester.root;
				StudentNode* q = semnode->semester.root;
				while (p != NULL){
					q = p;
					if (stuname < p->student.stname)
						p = p->left;
					else if (stuname > p->student.stname)
						p = p->right;
					else
						return false;
				}
				if (stuname < q->student.stname)
					q->left = new StudentNode(stu);
				else
					q->right = new StudentNode(stu);
				return true;
			}
		}
		semnode = semnode->snext;
	}
	return false;
}
//	1
bool Database::addtopic(string semname, string n, Topic tp){
	SemesterNode *semnode = shead;
	while (semnode != NULL){
		if (semnode->semester.sname == semname){
			StudentNode *stunode = semnode->semester.root;
			while (stunode != NULL){
				if (n < stunode->student.stname)
					stunode = stunode->left;
				else if (n > stunode->student.stname)
					stunode = stunode->right;
				else if (n == stunode->student.stname){
					TopicNode *tpnode = new TopicNode(tp);
					TopicNode *cnode = stunode->student.thead;
					if (cnode == NULL){
						tpnode->tnext = stunode->student.thead;
						stunode->student.thead = tpnode;
						return true;
					}
					else{
						while (cnode->tnext != NULL)
							cnode = cnode->tnext;
						tpnode->tnext = cnode->tnext;
						cnode->tnext = tpnode;
						return true;
					}
				}
			}
		}
		semnode = semnode->snext;
	}
	return false;
}
//	2
void Database::seetree(StudentNode *stnode, int imp){
	if (stnode->left != NULL)
		seetree(stnode->left, imp);

	TopicNode *tpnode = stnode->student.thead;
	while (tpnode != NULL){
		if (tpnode->topic.importance > imp)
			cout << tpnode->topic.tname << endl;
		tpnode = tpnode->tnext;
	}

	if (stnode->right != NULL)
		seetree(stnode->right, imp);
}
void Database::showtopics(int imp){
	SemesterNode *semnode = shead;
	while (semnode != NULL){
		StudentNode *stunode = semnode->semester.root;
		seetree(stunode, imp);
		semnode = semnode->snext;
	}
}
//	3
bool Database::findstu(string stuname, SemesterNode *semnode){
	if (semnode->semester.root == NULL)
		return false;
	else {
		StudentNode *p = semnode->semester.root;
		while (p != NULL){
			if (stuname < p->student.stname)
				p = p->left;
			else if (stuname > p->student.stname)
				p = p->right;
			else
				return true;
		}
		return false;
	}
}
StudentNode *Database::findstud(string stuname, SemesterNode *semnode){
	if (semnode->semester.root == NULL){
		return NULL;
	}
	else{
		StudentNode *p = semnode->semester.root;
		while (p != NULL){
			if (stuname < p->student.stname)
				p = p->left;
			else if (stuname > p->student.stname)
				p = p->right;
			else
				return p;
		}
		return NULL;
	}
}
void TopicList::addtolist(Topic topic){
	TopicNode *tp = new TopicNode(topic);
	tp->tnext = topichead;
	topichead = tp;
}
bool TopicList::searchfortp(TopicNode *tpnode){
	TopicNode *tp = topichead;
	while (tp != NULL && tp->topic.tname != tpnode->topic.tname)
		tp = tp->tnext;
	if (tp != NULL)
		return true;
	return false;
}
void TopicList::printlist(){
	int num = 0;
	TopicNode *top = topichead;
	while (top != NULL){
		cout << top->topic.tname << endl;
		top = top->tnext;
		num++;
	}
	cout << "number of topics in the list " << num << endl;
}
void Database::thirdfunct(string stuname, TopicList &tplist, Database &datab){
	for (SemesterNode *i = shead; i != NULL; i = i->snext){
		for (SemesterNode *j = i->snext; j != NULL; j = j->snext){
			if (datab.findstu(stuname, i) && datab.findstu(stuname, j)){
				StudentNode *sti = findstud(stuname, i);
				StudentNode *stj = findstud(stuname, j);
				for (TopicNode *t = sti->student.thead; t != NULL; t = t->tnext){
					for (TopicNode *m = stj->student.thead; m != NULL; m= m->tnext){
						if (t->topic.tname == m->topic.tname){
							if (!tplist.searchfortp(t))
								tplist.addtolist(t->topic);
						}
					}
				}
			}
		}
	}
	tplist.printlist();
}
//	4
void Database::searchtree(StudentNode *stnode, int ag){
	if (stnode->left != NULL)
		searchtree(stnode->left, ag);
	if (stnode->student.age > ag)
		cout << stnode->student.stname << endl;
	if (stnode->right != NULL)
		searchtree(stnode->right, ag);
}
void Database::showstudents(string n, int ag){
	SemesterNode *semnode = shead;
	while (semnode != NULL){
		if (semnode->semester.sname == n){
			StudentNode *stunode = semnode->semester.root;
			searchtree(stunode, ag);
		}
		semnode = semnode->snext;
	}
}
//	5
void Database::del(StudentNode *&q){
	StudentNode *p;
	if (q == NULL)
		delete q;
	if (q->left == NULL){
		p = q;
		q = q->right;
		delete p;
		return;
	}
	if (q->right == NULL){
		p = q;
		q = q->left;
		delete p;
		return;
	}
	p = q->left;
	while (p->right != NULL)
		p = p->right;
	p->right = q->right;
	p = q;
	q = q->left;
	delete p;
}
void Database::delstudent(string n){
	SemesterNode *semnode = shead;
	while (semnode != NULL){
		if (semnode->semester.root == NULL)
			break;
		StudentNode *p = semnode->semester.root;
		StudentNode *parent = NULL;
		while (p != NULL){
			if (p->student.stname == n)  
				break;
			parent = p;
			if (p->student.stname < n)  	
				p = p->right;
			else		
				p = p->left;
		}
		if (p == NULL)
			break;
		if (parent == NULL)
			del(semnode->semester.root);
		else if (parent->left == p)
			del(parent->left);
		else
			del(parent->right);
		semnode = semnode->snext;
	}
}