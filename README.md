#include <iostream>
#include <fstream> //Dosya okuma-yazma yapmak için 
#include <vector> //dynamic array için tercih ettiğim "vector" sınıfı için kütüphane 

//"vector" sınıfını kullanma sebebim otomatik olarak dizi boyutunu büyütüp küçülttüğü için

#include <cstdlib>
#include <ctime> //srand(time(0)) kısmı için

using namespace std;

// Random matris oluşturmak için:
vector<vector<int>> generateMatrix(int size, int min_Value, int max_Value) {
    vector<vector<int>> matrix(size, vector<int>(size, 0));
    for (int i = 0; i < size; ++i) {
        for (int j = 0; j < size; ++j) {
            matrix[i][j] = rand() % (max_Value - min_Value + 1) + min_Value;
        }
    }
    return matrix;
}

// Özel matrisi gerçekleştirmek için
vector<vector<int>> performSpecialOperation(const vector<vector<int>>& inputMatrix, const vector<vector<int>>& coreMatrix) {
    int inputSize = inputMatrix.size();
    int coreSize = coreMatrix.size();
    int outputSize = inputSize - coreSize + 1;

    vector<vector<int>> resultMatrix(outputSize, vector<int>(outputSize, 0));

    for (int row = 0; row < outputSize; ++row) {
        for (int col = 0; col < outputSize; ++col) {
            for (int i = 0; i < coreSize; ++i) {
                for (int j = 0; j < coreSize; ++j) {
                    resultMatrix[row][col] += inputMatrix[row + i][col + j] * coreMatrix[i][j];
                }
            }
        }
    }

    return resultMatrix;
}

// Matris yazdırmak için
void displayMatrix(const vector<vector<int>>& matrix) {
    for (const auto& row : matrix) {
        for (int value : row) {
            cout << value << "\t";
        }
        cout << endl;
    }
    cout << endl;
}

int main() {

    for (;;) {
        srand(time(0));

        int option;
        cout << "1 - (Generate new matrices)\n2 - (Read matrices from file)\n";
        cout << "Choose option: ";
        cin >> option;

        vector<vector<int>> inputMatrix, coreMatrix;

        if (option == 1) {
            // Yeni matris oluşturmak için
            int inputSize = rand() % 16 + 5; // 5 ile 20 arasında rastgele boyut ayarlaması için 
            int coreSize = rand() % 4 + 2;   // 2 ile 5 arasında rastgele boyut ayarlaması için 

            inputMatrix = generateMatrix(inputSize, 10, 20);
            coreMatrix = generateMatrix(coreSize, 1, 10);

            // Matrisleri dosyaya kaydetmek için
            ofstream Datafile("data.txt");
            if (Datafile.is_open()) {
                for (const auto& row : inputMatrix) {
                    for (int value : row) {
                        Datafile << value << " ";
                    }
                    Datafile << endl;
                }
                Datafile << endl;
                for (const auto& row : coreMatrix) {
                    for (int value : row) {
                        Datafile << value << " ";
                    }
                    Datafile << endl;
                }
                Datafile.close();
            }
            else {
                cout << "Error opening file for writing." << endl;
                return 1;
            }
        }
        else if (option == 2) {
            // Kaydettiğimiz dosyadan matrisleri oku
            ifstream Datafile("data.txt");
            if (Datafile.is_open()) {
                int value;
                for (Datafile >> value) {
                    inputMatrix.push_back({ value });
                }
                for (Datafile >> value) {
                    coreMatrix.push_back({ value });
                }
                Datafile.close();
            }
            else {
                cout << "File not found" << endl;
                option = 1;
            }
        }
        else {
            cout << "Invalid option." << endl;
            return 1;
        }

        // Matrisleri yazdırmak için
        cout << "Input Matrix:" << endl;
        displayMatrix(inputMatrix);

        cout << "Core Matrix:" << endl;;
        displayMatrix(coreMatrix);

        // Matris işlemi için
        vector<vector<int>> resultMatrix = performSpecialOperation(inputMatrix, coreMatrix);

        cout << "Output Matrix:" << endl;
        displayMatrix(resultMatrix);
    }
        return 0;
    
}
