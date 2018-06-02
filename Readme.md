#Converter Tool

#include <bits/stdc++.h>
using namespace std;

#define REP(i, a) for (int i = 0; i < a; i++)
#define REPP(i, a, b) for (int i = a; i < b; i++)
typedef pair<int, int> ii;
typedef vector<int> vi;
typedef vector<ii> vii;

#define INF 0x3f3f3f3f

int ntickets;
int price, ncities;
int city;
int nitineraries;

vi tickets[20]; // each ticket has a list of cities in order
int prices[20]; // each ticket's price
vi buy[200]; // index of tickets purchaseable at the city
vi itinerary;

map<int, int> id2idxmp; // city id to index map
map<ii, int> mp;

vi prevtickets; // prev tickets
vi bestidxs; // biggest indices

void addcity(int c) {
    if (id2idxmp.count(c)) return;

    int idx = id2idxmp.size();
    id2idxmp[c] = idx;
}

int index(int c) {
    return id2idxmp[c];
}

pair<int, ii> pp(int cost, int ticketidx, int neededidx) {
    return make_pair(cost, make_pair(ticketidx, neededidx));
}

pair<int, pair<ii, vi> > ppp(int cost, int ticketidx, int neededidx, vi &path) {
    return make_pair(cost, make_pair(make_pair(ticketidx, neededidx), path));
}

int dijkstra() {
    if (itinerary.size() < 2) return 0;

    prevtickets.clear();
    bestidxs.clear();
    mp.clear();

    vi path;
    int st = itinerary[0];
    set<pair<int, pair<ii, vi> > > ppq; // cost, ticketidx, neededidx, path
    set<pair<int, ii> > pq; // cost, ticketidx, neededidx
    REP(i, buy[st].size()) {
        int tic = buy[st][i];
        pq.insert( pp(prices[tic], tic, 1) );
    }

    int cost, ticketidx, neededidx, city, curneededidx;
    while (!pq.empty()) {
        set<pair<int, ii> >::iterator it = pq.begin();
        cost = it->first, ticketidx = it->second.first, neededidx = it->second.second;
        pq.erase(it);

        curneededidx = neededidx;
        REPP(i, 1, tickets[ticketidx].size()) {
            city = tickets[ticketidx][i];
            if (itinerary[curneededidx] == city) {
                curneededidx++;
            }
            if (!mp.count(make_pair(city, cost))) {
                prevtickets.push_back(ticketidx);
                bestidxs.push_back(curneededidx);
                mp[make_pair(city, cost)] = prevtickets.size()-1;
            } else if (bestidxs[mp[make_pair(city, cost)]] < curneededidx) {
                prevtickets[mp[make_pair(city, cost)]] = ticketidx;
                bestidxs[mp[make_pair(city, cost)]] = curneededidx;
            }
            if (curneededidx == itinerary.size()) {
                return cost;
            }
            REP(j, buy[city].size()) {
                int tic = buy[city][j];
                pq.insert( pp(cost + prices[tic], tic, curneededidx) );
            }
        }
    }

    return -1;
}

void printtickets(int st, int ed, int cost2) {
    if (cost2 <= 0) return;
    int tused = prevtickets[mp[make_pair(ed, cost2)]];
    int nexted = tickets[tused][0];
    int nextcost2 = cost2 - prices[tused];
    if (nextcost2 == 0) {
        printf("%d", tused+1);
        return;
    }
    printtickets(st, nexted, nextcost2);
    printf(" %d", tused+1);
}

int main() {
    int tc = 0;
    while (scanf("%d", &ntickets), ntickets) {
        tc++;
        id2idxmp.clear();
        REP(i, 200) buy[i].clear();

        REP(i, ntickets) {
            scanf("%d%d", &price, &ncities);
            prices[i] = price;
            tickets[i].clear();
            REP(j, ncities) {
                scanf("%d", &city);
                addcity(city);
                tickets[i].push_back(index(city));
                if (j == 0) buy[index(city)].push_back(i);
            }
        }

        scanf("%d", &nitineraries);
        REP(i, nitineraries) {
            itinerary.clear();
            scanf("%d", &ncities);
            REP(j, ncities) {
                scanf("%d", &city);
                addcity(city);
                itinerary.push_back(index(city));
            }
            int cost = dijkstra();
            printf("Case %d, Trip %d: Cost = %d\n", tc, i+1, cost);
            printf("  Tickets used: ");
            printtickets(itinerary[0], itinerary[itinerary.size()-1], cost);
            printf("\n");
        }
    }
    return 0;
}










Preview Image -
![alt text](http://poyser.pw/projects/project_data/8/screenshots/screenshot_59724342f1839.png)
