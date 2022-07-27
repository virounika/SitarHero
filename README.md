# SitarHero
//
//  hero.cpp
//  hero
//
//  Created by Virounika Mina on 4/24/22.
//

#include <iostream>
#include <cctype>
#include <string>
using namespace std;

bool hasProperSyntax(string tune) {
    if (tune.empty()) { // empty tune
        return true;
    }

    if (tune.size() == 1) {
        if (tune == "/") { // tune consisting of only slash
            return true;
        } else {
            return false;
        }
    }
     
    if (isdigit(tune.at(0))) { // tune can't be just digit
        return false;
    }
    
    if (tune.at(tune.size() - 1) != '/') { // tune must end in slash
        return false;
    }
     
    int consecutiveDigits = 0;
    
    for (int i = 0; i < (tune.size() - 1); i++) {
        if (tune.at(i) == '/') {
            consecutiveDigits = 0;
            if (tune.at(i + 1) != '/' && !isalpha(tune.at(i + 1))) {
                return false;
            }
        }
        else if (isalpha(tune.at(i))) {
            consecutiveDigits = 0;
            if ((tolower(tune.at(i)) != 'g' && // return false if not correct color
                 tolower(tune.at(i)) != 'r' &&
                 tolower(tune.at(i)) != 'y' &&
                 tolower(tune.at(i)) != 'b' &&
                 tolower(tune.at(i)) != 'o') || // also return false if char after color is not a slash or number
                (tune.at(i + 1) != '/' && !isdigit(tune.at(i + 1)))) {
                return false;
            }
        }
        else if (isdigit(tune.at(i))) {
            if (consecutiveDigits >= 2) { // more than 2 consecutive digits (started counting at 0)
                return false;
            }
            consecutiveDigits++; // will become 1 if it enters this else if
            if ((tune.at(i + 1) != '/' && !isdigit(tune.at(i + 1)))) { // digit must be followed by slash or digit
                return false;
            }
        }
        else { // if anything other than digit, letter, or slash
            return false;
        }
    }
    
    return true; // if all conditions don't return false hasProperSyntax returns true
}

// now check if tune is convertible
// remember this only outputs a number (!!!! return 0, 1, 2, 3, 4)

int isConvertible(string tune, int& badBeat) {
    
    int beatNumber = 0;
    
    for (int i = 0; i < tune.size(); i++) {
        // Increment the number of beats upon finding a '/':
        if (tune.at(i) == '/') {
            beatNumber++;
        }
        else if (isdigit(tune.at(i))) {
            // Parse the number of beats after a letter:
            int digit = 0;
            while (isdigit(tune.at(i))) {
                digit *= 10;                // increment i in this loop
                digit += tune.at(i) - '0'; // if 2 digits, sum them up
                i++;
            }
            if (digit <= 1) {
                // if the tune has digit less than 2:
                badBeat = beatNumber + 1;
                return 3;
            }
            // Ensure that there are an appropriate number of '/'s after the number:
            for (int j = 0; j < digit; j++) {
                // If the tune ended prematurely:
                if (i >= tune.size()) {
                    badBeat = beatNumber + 1;
                    return 4;
                // If the beat ended prematurely:
                } else if (tune.at(i) != '/') {
                    badBeat = beatNumber + 1;
                    return 2;
                // Otherwise:
                } else {
                    beatNumber++;
                    i++;
                }
            }
        } else {
            continue;
        }
    }
    // If no issues were encountered:
    return 0;
}

int convertTune(string tune, string& instructions, int& badBeat){
    if (!hasProperSyntax(tune)) { // improper syntax returns a 1
        return 1;
    }
    int result = isConvertible(tune, badBeat); // if not convertible, return result
    if (result != 0) {
        return result;
    }
    
    instructions = "";
    
    for (int i = 0; i < tune.size(); i++) {
        char current = tune.at(i);
        if (isalpha(tune.at(i))) {
            if (isdigit(tune.at(i + 1))) {
                int digit = tune.at(i + 1) - '0';
                if (isdigit(tune.at(i + 2))) {
                    digit *= 10;
                    digit += tune.at(i + 2) - '0';
                    i++;
                }
                i += digit + 1;
                for (int j = 0; j < digit; j++) {
                    instructions += toupper(current);
                }
            } else {
                instructions += tolower(current); // done if there is no digit after letter (turns letter lower)
                i++;
            }
        } else { // if not letter
            instructions += 'x';
        }
    }
    
    return result;
}

int main() {
    string tune;
    cout << "Enter a tune: ";
    getline(cin, tune);
    cout << boolalpha;
    
    // cout << hasProperSyntax(a) << endl;
    int badBeat = 324234;
    // cout << isConvertible(tune, badBeat) << endl;
    // cout << badBeat << endl;
    
    string instructions = "";
    cout << convertTune(tune, instructions, badBeat) << endl;
    cout << instructions << endl;
    cout << badBeat << endl;
}
