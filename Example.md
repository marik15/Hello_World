#include <fstream>
#include <vector>
#include <ctime>

using namespace std;

int N, M, Bye_index = 0, Ns = 50, Start = 0, Step = 10, Finish = 100;
unsigned long long int Attempts, Suggestions, Renouncements;
vector <vector <int>> Man_Prefs, Woman_Prefs;
vector <vector <bool>> Suggs;
vector <bool> Is_Married, Is_Alone;
vector <int> Friends, Friends_Pos, Husbands;

void Create_Prefs(vector <vector <int>> & Man_Prefs, vector <vector <int>> & Woman_Prefs, int Size) {
	Man_Prefs.resize(Size);
	Woman_Prefs.resize(Size);
	for (int i = 0; i < Size; ++i) {
		Man_Prefs[i].resize(Size);
		Woman_Prefs[i].resize(Size);
	}
	for (int i = 0; i < Size; ++i)
		for (int j = 0; j < Size; ++j) {
			Man_Prefs[i][j] = j;
			Woman_Prefs[j][i] = i;
		}
	for (int i = 0; i < Size; ++i)
		for (int j = 0; j < Size; ++j) {
			swap(Man_Prefs[i][j], Man_Prefs[i][rand() % Size]);
			swap(Woman_Prefs[j][i], Woman_Prefs[j][rand() % Size]);
		}
}

int main() {
	srand(time(NULL));
	for (int L = Start; L < Finish; L += Step) {
		Attempts = 0;
		Suggestions = 0;
		Renouncements = 0;
		for (int t = 0; t < Ns; ++t) {
			N = M = L + 1;
			Create_Prefs(Man_Prefs, Woman_Prefs, N);
			bool Flag = true;
			Suggs.resize(M);
			Is_Married.resize(N);
			Is_Alone.resize(N);
			Friends.resize(M);
			Friends_Pos.resize(M);
			Husbands.resize(M);
		    for (int i = 0; i < N; ++i) {
				Is_Married[i] = false;
				Is_Alone[i] = false;
		    }
		    for (int i = 0; i < M; ++i) {
		        Suggs[i].resize(N);
		        for (int j = 0; j < N; ++j)
		        	Suggs[i][j] = false;
				Friends[i] = -1;
				Friends_Pos[i] = -1;
				Husbands[i] = -1;
			}
		    while (Flag) {
		    	++Attempts;
		    	for (int i = 0; i < N; ++i) {
		    		if ((!Is_Married[i]) && (!Is_Alone[i])) {
		    			int k = 0, j = -1;
		    			while (k != M) {
		    				if ((Man_Prefs[i][k] != -1) && (Husbands[Man_Prefs[i][k]] == -1)) {
		    					j = k;
		    					k = M - 1;
							}
							++k;
						}
		    			if (j != -1) {
		    				Suggs[Man_Prefs[i][j]][i] = true;
		    				++Suggestions;
		    			}
		    			else
		    				Is_Alone[i] = true;
					}
				}
				for (int i = 0; i < M; ++i) {
					if (Husbands[i] == -1) {
						int k = 0, j = -1;
						while (k < N) {
							if (Suggs[i][Woman_Prefs[i][k]]) {
								if ((j == -1) && ((Friends[i] == -1) || (k < Friends_Pos[i])))
									j = k;
								else {
									Bye_index = 0;
									while (Man_Prefs[Woman_Prefs[i][k]][Bye_index] != i)
										++Bye_index;
									Man_Prefs[Woman_Prefs[i][k]][Bye_index] = -1;
									++Renouncements;
								}
							}
							++k;
						}
						if ((j != -1) && (j != N)) {
							if (Friends[i] != -1) {
								Bye_index = 0;
								while (Man_Prefs[Friends[i]][Bye_index] != i)
									++Bye_index;
								Man_Prefs[Friends[i]][Bye_index] = -1;
								Is_Married[Friends[i]] = false;
								++Renouncements;
							}
							Friends[i] = Woman_Prefs[i][j];
							Friends_Pos[i] = j;
							Is_Married[Friends[i]] = true;
							if (j == 0)
								Husbands[i] = Woman_Prefs[i][0];
						}
					}
				}
				Flag = false;
				for (int i = 0; i < N; ++i) {
					if ((!Is_Married[i]) && (!Is_Alone[i]))
						Flag = true;
					for (int j = 0; j < M; ++j)
						Suggs[j][i] = false;
				}
			}
		}
		ofstream fout("Pairs.txt", ios_base::out | ios_base::app);
		fout << L << " " << (double) Attempts/Ns << " " << (double) Suggestions/Ns << " " << (double) Renouncements/Ns << " \n";
		fout.close();
	}
	return 0;
}
