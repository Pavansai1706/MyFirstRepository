#include<iostream>
#include<cstdlib>
#include<ctime>

class Character {
private:
    float health;
    float maxHealth;
    float basePower;
    float baseHealing;

public:
    Character(float _health, float _basePower, float _baseHealing) : health(_health), maxHealth(_health), basePower(_basePower), baseHealing(_baseHealing) {
        srand(static_cast<unsigned int>(time(0)));
    }

    virtual ~Character() {}

    bool IsAlive() {
        return health > 0;
    }

    float Attack() {
        return basePower + GetAdditionalDamage();
    }

    virtual float GetAdditionalDamage() {
        return 0;
    }

    void TakeDamage(float damage) {
        health -= damage;
    }

    void Heal() {
        if (IsOverHeal()) {
            std::cout << "\nOverHealing... Wasted Chance!!!";
            return;
        }
        float effectiveHealing = GetEffectiveHealing();
        std::cout << "\nHealed " << effectiveHealing;

        health += effectiveHealing;
        if (IsOverHeal())
            health = maxHealth;
    }

    bool IsOverHeal() {
        return health >= maxHealth;
    }

    float GetEffectiveHealing() {
        return (rand() % 30) + baseHealing;
    }

    void ShowStats() {
        std::cout << "\nHealth : " << health;
        std::cout << "\nAttack Power : " << basePower;
        std::cout << "\nBase Healing : " << baseHealing;
    }
};

class Naruto : public Character {
public:
    Naruto() : Character(800, 80, 40) {
        std::cout << "\nNaruto - Hokage, The Hero of Hidden Leaf";
    }

    float GetAdditionalDamage() override {
        float additionalDamage = rand() % 60;
        std::cout << "\nAdditional Damage " << additionalDamage << " is Dealt";
        return additionalDamage;
    }

    virtual ~Naruto() {}
};

class Sasuke : public Character {
public:
    Sasuke() : Character(600, 70, 35) {
        std::cout << "\nSasuke - Avenger, Last of the Uchiha";
    }

    float GetAdditionalDamage() override {
        float additionalDamage = rand() % 50;
        std::cout << "\nAdditional Damage " << additionalDamage << " is Dealt";
        return additionalDamage;
    }

    virtual ~Sasuke() {}
};

class Shikamaru : public Character {
public:
    Shikamaru() : Character(400, 40, 20) {
        std::cout << "\nShikamaru - The Lazy Genius";
    }

    float GetAdditionalDamage() override {
        float additionalDamage = rand() % 30;
        std::cout << "\nAdditional Damage " << additionalDamage << " is Dealt";
        return additionalDamage;
    }

    virtual ~Shikamaru() {}
};

class BattleAdventure {
private:
    Character* players[3];

    void ShowInstructions() {
        std::cout << "\n\n";
        std::cout << "!! Welcome To 2-Player-Battle-Adventure Game !!\n\n\n";
        std::cout << "Instructions :\n";
        std::cout << "1) Each player has one choice at a time.\n";
        std::cout << "2) The player can either attack or heal.\n";
        std::cout << "3) The player whose health goes below 0 will die and lose the game.\n";
        std::cout << "4) Press 'H' to heal and 'A' to attack.\n";
    }

    void ShowPlayerTypeInstruction() {
        std::cout << "\n\nPlayer types :\n";
        std::cout << "1) Naruto      - Hokage, The Hero of Hidden Leaf\n";
        std::cout << "2) Sasuke      - Avenger, Last of the Uchiha\n";
        std::cout << "3) Shikamaru   - The Lazy Genius\n";
    }

    void ShowCurrentStats() {
        std::cout << "\n\nPlayer 1 Stats";
        players[1]->ShowStats();
        std::cout << "\nPlayer 2 Stats";
        players[2]->ShowStats();
    }

    bool PlayAgainChoice() {
        char choice;
        std::cout << "\n\nPress 'P' To Play Again. Choice : " << std::endl;
        std::cin >> choice;
        return (choice == 'p' || choice == 'P') ? true : false;
    }

    void ClearPlayers() {
        delete players[1];
        delete players[2];
    }

    void PlayerSelection() {
        for (int playerTurn = 1; playerTurn <= 2; playerTurn++) {
            ShowPlayerTypeInstruction();
            int typeChoice;
            do {
                std::cout << "\nPlayer " << playerTurn << " : Select player type (1, 2, 3)\n";
                std::cout << "Player Type : ";
                std::cin >> typeChoice;
                switch (typeChoice) {
                case 1:
                case 2:
                case 3:
                    CreatePlayer(playerTurn, typeChoice);
                    break;
                default:
                    std::cout << "\nWRONG choice.. Try again!!\n";
                }
            } while (typeChoice < 1 || typeChoice > 3);
        }
    }

    void CreatePlayer(int playerTurn, int type) {
        std::cout << "\nPlayer " << playerTurn << " Selected " << type;
        players[playerTurn] = GetCharacterOfType(type);
        players[playerTurn]->ShowStats();
    }

    Character* GetCharacterOfType(int type) {
        switch (type) {
        case 1:
            return new Naruto();
        case 2:
            return new Sasuke();
        case 3:
            return new Shikamaru();
        default:
            return nullptr;
        }
    }

    bool IsPlayersAlive() {
        return players[1]->IsAlive() && players[2]->IsAlive();
    }

    void Battle() {
        bool isPlayer2Turn = false;
        while (IsPlayersAlive()) {
            ShowCurrentStats();
            std::cout << "\n\nPlayer " << isPlayer2Turn + 1 << ", Choose your Action \nPress 'H' to heal and 'A' to attack.\nAction : ";
            bool isActionValid;
            do {
                isActionValid = true;
                char choice;
                std::cin >> choice;
                switch (choice) {
                case 'a':
                case 'A':
                    players[(!isPlayer2Turn) + 1]->TakeDamage(players[isPlayer2Turn + 1]->Attack());
                    break;
                case 'h':
                case 'H':
                    players[isPlayer2Turn + 1]->Heal();
                    break;
                default:
                    std::cout << "\nInvalid Action. Try again!!";
                    isActionValid = false;
                    break;
                }
            } while (!isActionValid);
            isPlayer2Turn = !isPlayer2Turn;
        }
        GameOver((!isPlayer2Turn) + 1);
    }

    void GameOver(int playerIndex) {
        std::cout << "\nPlayer " << playerIndex << " Wins";
    }

public:
    void Run() {
        bool play = true;
        while (play) {
            ShowInstructions();
            PlayerSelection();
            Battle();
            ClearPlayers();
            play = PlayAgainChoice();
        }
    }
};

int main() {
    BattleAdventure newGame;
    newGame.Run();
    return 0;
}
