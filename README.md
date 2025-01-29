import random
import json

# Lijst met willekeurige botnamen
BOT_NAMEN = ["AlphaBot", "CyberFist", "RoboHand", "MechaMind", "IronGrip", "SteelPalm", "ByteCrusher", "NeuralFist"]

# Highscore bestand
HIGH_SCORE_FILE = "highscores.json"

def load_highscores():
    try:
        with open(HIGH_SCORE_FILE, "r") as file:
            return json.load(file)
    except (FileNotFoundError, json.JSONDecodeError, PermissionError):
        return []

def save_highscores(highscores):
    try:
        with open(HIGH_SCORE_FILE, "w") as file:
            json.dump(highscores, file)
    except Exception as e:
        print(f"⚠️ Kan highscores niet opslaan: {e}")

def get_computer_choice(difficulty):
    choices = ["rock", "paper", "scissors"]
    if difficulty == "makkelijk":
        return random.choice(choices + ["rock"] * 2)
    elif difficulty == "moeilijk":
        return random.choice(choices + ["paper", "scissors"] * 2)
    return random.choice(choices)

def determine_winner(player, computer):
    if player == computer:
        return "Gelijkspel!"
    elif (player == "rock" and computer == "scissors") or \
         (player == "scissors" and computer == "paper") or \
         (player == "paper" and computer == "rock"):
        return "Je wint!"
    else:
        return "Je verliest!"

def normal_mode():
    print("Je speelt tegen de computer!")
    player_choice = input("Kies rock, paper of scissors: ").strip().lower()
    if player_choice not in ["rock", "paper", "scissors"]:
        print("Ongeldige keuze, probeer opnieuw.")
        return normal_mode()
    computer_choice = get_computer_choice("gemiddeld")
    print(f"De computer koos: {computer_choice}")
    print(determine_winner(player_choice, computer_choice))

def tournament_mode():
    print("Welkom bij het toernooi!")
    player_name = input("Voer je naam in: ")
    rounds = ["Round of 16", "Quarter-finals", "Semi-finals", "Final"]
    for round_name in rounds:
        opponent = random.choice(BOT_NAMEN)
        print(f"{round_name}: {player_name} vs {opponent}")
        player_choice = input("Kies rock, paper of scissors: ").strip().lower()
        if player_choice not in ["rock", "paper", "scissors"]:
            print("Ongeldige keuze, probeer opnieuw.")
            return tournament_mode()
        computer_choice = get_computer_choice("gemiddeld")
        print(f"{opponent} koos: {computer_choice}")
        if determine_winner(player_choice, computer_choice) == "Je wint!":
            print(f"Gefeliciteerd! Je hebt {round_name} gewonnen!")
        else:
            print(f"Helaas! Je bent uitgeschakeld door {opponent}. Game over!")
            return
    print("Gefeliciteerd! Je hebt het toernooi gewonnen!")

def endless_mode():
    print("Welkom bij de Eindeloos-modus!")
    player_name = input("Voer je naam in: ")
    difficulty_levels = ["makkelijk", "gemiddeld", "moeilijk"]
    level = 0  # Start op de makkelijkste moeilijkheidsgraad
    score = 0

    while True:
        difficulty = difficulty_levels[min(level, len(difficulty_levels)-1)]
        opponent = random.choice(BOT_NAMEN)
        print(f"{player_name} vs {opponent} (Moeilijkheid: {difficulty})")
        player_choice = input("Kies rock, paper of scissors: ").strip().lower()
        if player_choice not in ["rock", "paper", "scissors"]:
            print("Ongeldige keuze, probeer opnieuw.")
            continue

        computer_choice = get_computer_choice(difficulty)
        print(f"{opponent} koos: {computer_choice}")
        result = determine_winner(player_choice, computer_choice)
        print(result)

        if result == "Je wint!":
            score += 1
            level += 1  # Moeilijkheidsgraad verhogen per overwinning
            print(f"Score: {score}")
        else:
            print(f"Je eindscore is: {score}")
            highscores = load_highscores()
            highscores.append((player_name, score))
            highscores.sort(key=lambda x: x[1], reverse=True)
            save_highscores(highscores)
            break

def view_highscores():
    print("Highscores:")
    highscores = load_highscores()
    if not highscores:
        print("Nog geen highscores beschikbaar.")
    else:
        for hs in highscores[:5]:
            print(f"{hs[0]}: {hs[1]}")

def main_menu():
    while True:
        print("\nHoofdmenu")
        print("1. Normaal modus")
        print("2. Toernooi modus")
        print("3. Eindeloos modus")
        print("4. Highscores bekijken")
        print("5. Stoppen")
        keuze = input("Maak een keuze: ").strip()
        if keuze == "1":
            normal_mode()
        elif keuze == "2":
            tournament_mode()
        elif keuze == "3":
            endless_mode()
        elif keuze == "4":
            view_highscores()
        elif keuze == "5":
            print("Bedankt voor het spelen!")
            break
        else:
            print("Ongeldige keuze, probeer opnieuw.")

if __name__ == "__main__":
    main_menu()


