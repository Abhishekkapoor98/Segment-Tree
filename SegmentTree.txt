#include<bits/stdc++.h>
using namespace std;

#ifndef ONLINE_JUDGE
#define debug(x) cerr << #x<<" "; _print(x); cerr << endl;
#else
#define debug(x);
#endif

typedef long long ll;
typedef unsigned long long ull;
typedef long double lld;
// typedef tree<pair<int, int>, null_type, less<pair<int, int>>, rb_tree_tag, tree_order_statistics_node_update > pbds; // find_by_order, order_of_key

void _print(ll t) {cerr << t;}
void _print(int t) {cerr << t;}
void _print(string t) {cerr << t;}
void _print(char t) {cerr << t;}
void _print(lld t) {cerr << t;}
void _print(double t) {cerr << t;}
void _print(ull t) {cerr << t;}

template <class T, class V> void _print(pair <T, V> p);
template <class T> void _print(vector <T> v);
template <class T> void _print(set <T> v);
template <class T, class V> void _print(map <T, V> v);
template <class T> void _print(multiset <T> v);
template <class T, class V> void _print(pair <T, V> p) {cerr << "{"; _print(p.ff); cerr << ","; _print(p.ss); cerr << "}";}
template <class T> void _print(vector <T> v) {cerr << "[ "; for (T i : v) {_print(i); cerr << " ";} cerr << "]";}
template <class T> void _print(set <T> v) {cerr << "[ "; for (T i : v) {_print(i); cerr << " ";} cerr << "]";}
template <class T> void _print(multiset <T> v) {cerr << "[ "; for (T i : v) {_print(i); cerr << " ";} cerr << "]";}
template <class T, class V> void _print(map <T, V> v) {cerr << "[ "; for (auto i : v) {_print(i); cerr << " ";} cerr << "]";}

#define fast_io ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
#define lli long long int
#define mod 1000000007
#define PB push_back
#define PPB pop_back
#define MP make_pair
#define vi vector<int>
#define vc vector<char>
#define vlli vector<long long int>
#define uos unordered_set<int>
#define min_heap priority_queue<int,vector<int>,greater<int>>
#define max_heap priority_queue<int>
#define pii pair<int,int>
#define F first
#define S second
#define loop(a,b) for(lli i=a;i<=b;i++)
#define endl '\n'
#define ll long long

int t,n,val,node,q_l,q_r,l_node,r_node;
int arr[100005];
int seg[4*100005];
int lazy[4*100005];

int build_ST(int ind,int left,int right)
{
	if(left == right){
		return seg[ind] = arr[left];
	}
	else
	{
		int mid = (left+right)/2;
	
		int L = build_ST(2*ind+1,left,mid);
		int R = build_ST(2*ind+2,mid+1,right);
		
		return seg[ind] = (L+R);
	}
}

int search_ST(int ind,int left,int right,int q_l,int q_r)
{
	// completely lies
	if(left >=q_l && right <= q_r)
		return seg[ind];
	//dosen't lies
	else if(right < q_l || left > q_r)
		return 0;
	//overlaps
	else
	{
		int mid = (left + right)/2;
		int L = search_ST(2*ind+1,left,mid,q_l,q_r);
		int R = search_ST(2*ind+2,mid+1,right,q_l,q_r);
		return (L+R);
	}
}

void point_update(int ind,int left,int right,int node,int val)
{
	if(left == right)
		seg[ind] += val;
	else
	{
		int mid = (left + right)/2;
		//node lies to the left
		if(node >= left && node  <= mid)
			point_update(2*ind+1,left,mid,node,val);
		//node lies to the right
		else
			point_update(2*ind+2,mid+1,right,node,val);

		seg[ind] = (seg[2*ind+1] + seg[2*ind+2]);
	}
}

void range_update(int ind,int low,int high,int l,int r,int val)
{
	if(lazy[ind] != 0)
	{
		seg[ind] += (high - low + 1) * lazy[ind];
		if(low != high)
		{
			lazy[2*ind+1] += lazy[ind];
			lazy[2*ind+2] += lazy[ind];
		}
		lazy[ind] = 0;
	}

	//completely lies
	if(low >= l && high <= r)
	{
		seg[ind] += (high - low +1) * val;
		if(low != high)
		{
			lazy[2*ind+1] += val;
			lazy[2*ind+2] += val;
		}
		return;
	}
	//dosen't lies
	else if(r < low || l > high || low > high)
		return;
	//partially overlaps
	else
	{
		int mid = (low + high) >> 1;

		range_update(2*ind+1, low, mid, l, r, val);
		range_update(2*ind+2, mid+1, high, l, r, val);

		seg[ind] = (seg[2*ind+1] + seg[2*ind+2]);
		return;
	}
}

int search_ST_lazy(int ind,int low,int high,int l,int r)
{
	if(lazy[ind] != 0)
	{
		seg[ind] = (high - low + 1)*lazy[ind];

		if(low != high)
		{
			lazy[2*ind+1] += lazy[ind];
			lazy[2*ind+2] += lazy[ind];
		}
		lazy[ind] = 0;
	}

	//completely lies
	if(low >= l && high <= r)
		return seg[ind];
	else if(low > high || l > high || r < low)
		return 0;
	else
	{
		int mid = (low+high)/2;

		return search_ST_lazy(2*ind+1,low,mid,l,r) + search_ST_lazy(2*ind+2,mid+1,high,l,r);
	}
}

void solve()
{
	cin>>t;

	while(t--)
	{
		cin>>n;

		for(int i=0;i<n;i++)
			cin>>arr[i];

		build_ST(0,0,n-1);

		cin>>l_node>>r_node>>val;

		for(int i=0;i<41;i++)
			lazy[i] = 0;
		range_update(0,0,n-1,l_node,r_node,val);

		// point_update(0,0,n-1,node,val);

		cin>>q_l>>q_r;
		// cout<<search_ST(0,0,n-1,q_l,q_r);

		cout<<search_ST_lazy(0,0,n-1,l_node,r_node);
	}
}

int main()
{
	fast_io;

#ifndef ONLINE_JUDGE
	freopen("input.txt","r",stdin);
	freopen("output.txt","w",stdout);
	freopen("error.txt","w",stderr);
#endif

	solve();

	return 0;
}
