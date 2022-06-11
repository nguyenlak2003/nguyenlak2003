#include <bits/stdc++.h>
#define ll long long
#define x first
#define y second
const long mod = 998244353;
using namespace std;
long n,q,a[1000005],cnt = 0;
struct data{
    long x,p;
}g[4000005];
ll f[4000005];
void build(long id, long l, long r){
    if( l == r ){
        g[id].x = a[l];
        g[id].p = r;
        return;
    }
    long mid = ( l + r ) / 2;
    build(id*2,l,mid);
    build(id*2+1,mid + 1,r);
    if( g[id*2].x < g[id*2+1].x ) g[id] = g[id*2];
    if( g[id*2].x > g[id*2+1].x ) g[id] = g[id*2+1];
    if( g[id*2].x == g[id*2+1].x ) g[id] = g[id*2];
}
void update(long id, long l, long r, long pos, long val){
    if( pos < l || pos > r ) return;
    if( l == r ){
        g[id].x = val;
        return;
    }
    long mid = ( l + r ) / 2;
    update(id*2,l,mid,pos,val);
    update(id*2+1,mid + 1,r,pos,val);
    if( g[id*2].x < g[id*2+1].x ) g[id] = g[id*2];
    if( g[id*2].x > g[id*2+1].x ) g[id] = g[id*2+1];
    if( g[id*2].x == g[id*2+1].x ) g[id] = g[id*2];
}
data get(long id, long l, long r, long u, long v){
    if( l > v || r < u ) return {long(1e9) + 7,long(1e9)};
    if( u <= l && r <= v ) return g[id];
    long mid = ( l + r ) / 2;
    data q1 = get(id*2,l,mid,u,v);
    data q2 = get(id*2+1,mid + 1,r,u,v);
    if( q1.x < q2.x ) return q1;
    if( q1.x > q2.x ) return q2;
    return q1;
}
void _build(long id ,long l, long r){
    if( l == r ){
        f[id] = a[l];
        return;
    }
    long mid = ( l + r ) / 2;
    _build(id*2,l,mid);
    _build(id*2 + 1,mid + 1,r);
    f[id] = f[id*2] + f[id*2+1];
}
void _update(long id, long l, long r, long pos, long val){
    if( l > pos || r < pos ) return;
    if( l == r ){
        f[id] = val;
        return;
    }
    long mid = ( l + r ) / 2;
    _update(id*2,l,mid,pos,val);
    _update(id*2+1,mid + 1,r,pos,val);
    f[id] = f[id*2] + f[id*2+1];
}
ll _get(long id, long l, long r, long u, long v){
    if( u > r || v < l ) return 0;
    if( u <= l && r <= v ) return f[id];
    long mid = ( l + r ) / 2;
    return _get(id*2,l,mid,u,v) + _get(id*2+1,mid+1,r,u,v);
}
int main(){
    ios_base::sync_with_stdio(false);cin.tie(0);cout.tie(0);
    cin >> n >> q;
    for(long i = 1; i <= n; i++) cin >> a[i];
    build(1,1,n);
    _build(1,1,n);
    while(q--){
        long type;
        cin >> type;
        if(type == 1){
            long d;
            cin >> d;
            cnt += d;
            if( cnt >= n ) cnt -= n;
        }
        if(type == 2){
            long l,r,val;
            cin >> l >> r >> val;
            l -= cnt; r -= cnt;
            if( l < 1 ) l += n;
            if( r < 1 ) r += n;
            if( l <= r ){
                data tmp = get(1,1,n,l,r);
                update(1,1,n,tmp.p,val);
                _update(1,1,n,tmp.p,val);
            }
            else{
                data q1 = get(1,1,n,l,n);
                data q2 = get(1,1,n,1,r);
                if( q1.x < q2.x ){
                    update(1,1,n,q1.p,val);
                    _update(1,1,n,q1.p,val);
                }
                if( q1.x > q2.x ){
                    update(1,1,n,q2.p,val);
                    _update(1,1,n,q2.p,val);
                }
                if( q1.x == q2.x ){
                    update(1,1,n,q1.p,val);
                    _update(1,1,n,q1.p,val);
                }
            }
        }
        if(type == 3){
            long l,r;
            cin >> l >> r;
            l -= cnt; r -= cnt;
            if( l < 1 ) l += n;
            if( r < 1 ) r += n;
            if( r < l ) cout << _get(1,1,n,l,n) + _get(1,1,n,1,r) << '\n';
            else cout << _get(1,1,n,l,r) << '\n';
        }
    }
}
