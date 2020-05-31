
#include<iostream>
#include<math.h>
using namespace std;
struct node {
	int ele[4], child[4];
};
class btree1
{
public:
	node* tree[10][10];
	int count[10], leaf, path[10];
	btree1();
	node* create_node();
	void insert(int);
	void m_search(int);
	void display();
	void insert_node(node*, int);
	void search(int);
	int search_node(node*, int);
	int nodefull(node*);
	void split(node*);
};
btree1::btree1() {
	leaf = -1;
	for (int i = 0; i < 10; i++) {
		count[i] = -1; 
		path[i] = -1;
	}
}
node* btree1::create_node() {
	node* n;
	n = new node;
	for (int i = 0; i < 4; i++) {
		n->ele[i] = -1;
		n->child[i] = -1;
	}
	return n;
}
void btree1::insert(int key) {
	int n, parent;
	node* first_node;
	if (leaf == -1)
	{
		first_node = create_node();
		tree[0][0] = first_node;
		leaf++;
		count[0]++;
		first_node->ele[0] = key;
	}
	else if (leaf == 0)
	{
		if (nodefull(tree[0][0]))
		{
			path[leaf] = 0;
			split(tree[0][0]);
			insert(key);
		}
		else
		{
			insert_node(tree[0][0], key);
		}
	}
	else
	{
		search(key);
		n = path[leaf];
		parent = path[leaf - 1];
		if ((nodefull(tree[leaf][n])))
		{
			split(tree[leaf][n]);
			insert(key);
		}
		else {
			insert_node(tree[leaf][n], key);
		}
	}
}
void btree1::m_search(int key)
{
	int flag = 0, i;
	node* node1;
	search(key);
	node1 = tree[leaf][path[leaf]];
	for (i = 0; node1->ele[i] != -1; i++) {
		if (node1->ele[i] == key)
		{
			flag = 1;
			break;
		}
	}
	cout << "\nthe path traveresed is:";
	for (i = 0; path[i] != -1; i++)
		cout << path[i] << "->";
	if (flag)
		cout << "elememnt found\n";
	else
		cout << "element not fund\n";
}
void btree1::display()
{
	int i, j, k;
	for (i = 0; i <= leaf; i++) {
		cout << "\nlevel---" << i << "\n";
		for (j = 0; j <= count[i]; j++) {
			if (i != leaf)//bptree
				k = 1;
			else
				k = 0;
			for (; tree[i][j]->ele[k] != -1; k++)//bptree
				cout << "||" << tree[i][j]->ele[k]<<"||";
			cout << "->\t";
		}
		cout << "\n";
	}
}
void btree1::search(int key)
{
	int i, j, temp;
	path[0] = 0;
	if (leaf) {
		j = 0;
		for (i = 0; i < leaf; i++) {
			temp = search_node(tree[i][j], key);
			path[i + 1] = temp;
			j = temp;
		}
	}
}
int btree1::search_node(node* node1, int key) 
{
	if(key <= node1->ele[0])//bptree
	return node1->child[0];
	for (int i = 1; i < 4; i++) {
		if (key <= node1->ele[i]||key>node1->ele[i+1])//bptree
			return node1->child[i];
		else if (node1->ele[i + 1] == -1)
			return node1->child[i];
	}
}
int btree1::nodefull(node* node1)
{
	if (node1->ele[3] != -1)
		return 1;
	else
		return 0;
}
void btree1::insert_node(node* node1, int key)
{
	int flag = 0, count = -1, i, j, t;
	node* n1;
	for (i = 0; i < 4; i++)
		if (node1->ele[i] != -1)
			++count;
	i = 0;
	while ((!flag)&&node1->ele[i] != -1) {
		if (node1->ele[i] > key)
		{
			flag = 1;
			for (j = count; j >= i; j--)
				node1->ele[j + 1] = node1->ele[j];
			node1->ele[i] = key;
		}
		i++;
	}
	if (!flag)
	{
		node1->ele[count + 1] = key;
		for (i = leaf - 1; i >= 0; i--) {
			n1 = tree[i][path[i]];
			for (t = 0; n1->ele[t] != -1; t++);
			n1->ele[t - 1] = key;
		}
	}
	for (i = 0; i <= count + 1; i++)
		cout << "\t" << node1->ele[i];
}
void btree1::split(node* oldnode)
{
	node* newnode, * n1, * n2;
	int i, j, k, x, y, t, n;
	newnode = create_node();
	newnode->ele[0] = oldnode->ele[2];
	newnode->ele[1] = oldnode->ele[3];
	oldnode->ele[2] = -1;
	oldnode->ele[3] = -1;
	t = count[leaf];
	n = path[leaf];
	for (i = t, j = t + 1; i > n; i--, j--)
		tree[leaf][j] = tree[leaf][i];
	tree[leaf][n + 1] = newnode;
	count[leaf]++;
	x = leaf;

	if ((count[leaf] + 1) == 1)
		t = 1;
	else
		t = (int)((int)log(count[leaf] + 1) / log(2));
	if (t != leaf)
	{
		++leaf;
		count[leaf] = count[x];
		for (i = 0; i <= count[leaf]; i++)
			std::swap(tree[leaf][i], tree[x][i]);
	}
	for (i = leaf - 1; i >= 0; i--)
		count[i] = -1;
	for (i = t, j = i - 1; i > 0; i--, j--)
	{
		for (k = 0; k < (count[i] + 1) / 2; k++) {
			n1 = tree[i][2 * k];
			n2 = tree[i][(2 * k) + 1];
			for (x = 0; n1->ele[x] != -1; x++);
			for (y = 0; n2->ele[y] != -1; y++);
			newnode = create_node();
			count[j]++;
			tree[j][count[j]] = newnode;
			newnode->ele[0] = n1->ele[x - 1];
			newnode->child[0] = 2 * k;
			newnode->ele[1] = n2->ele[y - 1];
			newnode->child[1] = ((2 * k) + 1);
		}
		if (count[i] != 1 && count[i] % 2 == 0)
		{
			n2 = tree[i][count[i]];
			for (y = 0; n2->ele[y] != -1; y++);
			newnode->ele[2] = n2->ele[y - 1];
			newnode->child[2] = count[i];
		}
	}
}
int main()
{
	btree1 bt;
	int ch, key;
	while (1)
	{
		cout << "1.insert\n2.search\n3.display\n4.exit\nenter ur choice\n";
		cin >> ch;
		switch (ch)
		{
		case 1:cout << "enter the element\n";
			cin >> key;
			bt.insert(key);
			break;
		case 2:cout << "enter the key\n";
			cin >> key;
			bt.m_search(key);
			break;
		case 3:bt.display();
			break;
		case 4:return 0;
		default:cout << "enterne[rpoper value\n";
		}
	}
	return 0;
}










