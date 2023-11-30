# projet
import random  # Importe le module random pour générer des nombres aléatoires.

# Cette fonction crée une carte 2D avec des murs et des espaces libres.
def generate_random_map(size, proportion_wall):
    m = [[0 for _ in range(size[1])] for _ in range(size[0])]  # Crée une grille 2D de taille size, initialement remplie de 0.
    for y in range(size[0]):  # Boucle sur les lignes de la grille.
        for x in range(size[1]):  # Boucle sur les colonnes de la grille.
            # Affecte 1 (mur) ou 0 (espace libre) à chaque cellule, basé sur la proportion_wall.
            m[y][x] = 1 if random.random() < proportion_wall else 0
    return m  # Retourne la grille générée.

# Crée un dictionnaire représentant le personnage avec sa position, son apparence et son score.
def create_perso(start_pos):
    return {"char": "o", "x": start_pos[0], "y": start_pos[1], "score": 0}  # Retourne un dictionnaire avec les informations du personnage.

# Définit les positions d'entrée et de sortie sur la carte.
def create_entry_exit(m):
    entry = (0, 0)  # Définit l'entrée en haut à gauche de la carte.
    exit = (len(m) - 1, len(m[0]) - 1)  # Définit la sortie en bas à droite de la carte.
    m[entry[0]][entry[1]] = 0  # Assure que l'entrée est un espace libre.
    m[exit[0]][exit[1]] = 0   # Assure que la sortie est un espace libre.
    return entry, exit  # Retourne les positions d'entrée et de sortie.

# Crée un ensemble de coordonnées pour les objets à collecter sur la carte.
def create_objects(nb_objects, m):
    objects = set()  # Initialise un ensemble vide pour les objets.
    while len(objects) < nb_objects:  # Continue jusqu'à ce que le nombre d'objets requis soit atteint.
        # Génère une position aléatoire sur la carte.
        x, y = random.randint(0, len(m[0]) - 1), random.randint(0, len(m) - 1)
        if m[y][x] == 0:  # Vérifie si la position est un espace libre.
            objects.add((x, y))  # Ajoute la position à l'ensemble des objets.
    return objects  # Retourne l'ensemble des positions des objets.

# Affiche la carte, le personnage, et les objets.
def display_map_and_char(m, p, objects):
    for y, line in enumerate(m):  # Parcourt chaque ligne de la carte.
        for x, cell in enumerate(line):  # Parcourt chaque cellule de la ligne.
            if (x, y) == (p["x"], p["y"]):  # Vérifie si la cellule est la position du personnage.
                print(p["char"], end="")  # Affiche le caractère du personnage.
            elif (x, y) in objects:  # Vérifie si la cellule contient un objet.
                print(".", end="")  # Affiche un point pour l'objet.
            else:
                # Affiche un espace pour un espace libre ou un # pour un mur.
                print(" " if cell == 0 else "#", end="")
        print()  # Passe à la ligne suivante après avoir affiché une ligne.
        
# Met à jour la position du personnage en fonction de l'entrée utilisateur.
def update_p(letter, p, m):
    x, y = p["x"], p["y"]  # Récupère la position actuelle du personnage.
    # Vérifie la direction et déplace le personnage si la case est libre.
    if letter == "z" and y > 0 and m[y - 1][x] == 0:
        p["y"] -= 1  # Déplace vers le haut si la case au-dessus est libre.
    elif letter == "s" and y < len(m) - 1 and m[y + 1][x] == 0:
        p["y"] += 1  # Déplace vers le bas si la case en dessous est libre.
    elif letter == "q" and x > 0 and m[y][x - 1] == 0:
        p["x"] -= 1  # Déplace vers la gauche si la case à gauche est libre.
    elif letter == "d" and x < len(m[0]) - 1 and m[y][x + 1] == 0:
        p["x"] += 1  # Déplace vers la droite si la case à droite est libre.
        
# Met à jour les objets collectés et le score du personnage.
def update_objects(p, objects):
    if (p["x"], p["y"]) in objects:  # Vérifie si le personnage est sur un objet.
        objects.remove((p["x"], p["y"]))  # Supprime l'objet de l'ensemble.
        p["score"] += 1  # Incrémente le score du personnage.

# Utilise une bombe pour enlever les murs autour de la position du personnage.
def use_bomb(p, m):
    x, y = p["x"], p["y"]  # Récupère la position actuelle du personnage.
    for i in range(-1, 2):  # Parcourt les lignes autour du personnage.
        for j in range(-1, 2):  # Parcourt les colonnes autour du personnage.
            # Vérifie si la position est dans les limites de la carte.
            if 0 <= x + j < len(m[0]) and 0 <= y + i < len(m):
                m[y + i][x + j] = 0  # Change la cellule en espace libre.

size = (10, 10)  # Définit la taille de la carte.
proportion_wall = 0.3  # Définit la proportion des murs sur la carte.
m = generate_random_map(size, proportion_wall)  # Génère la carte.
entry, exit = create_entry_exit(m)  # Crée les points d'entrée et de sortie.

p = create_perso(entry)  # Crée le personnage à la position d'entrée.
objects = create_objects(5, m)  # Place 5 objets sur la carte.

# Boucle principale du jeu.
while True:
    display_map_and_char(m, p, objects)  # Affiche la carte, le personnage et les objets.
    print(f"Score: {p['score']}")  # Affiche le score du personnage.
    # Demande au joueur la prochaine action.
    letter = input("Direction (z, q, s, d), 'e' pour bombe, ou 'exit' pour quitter: ")
    if letter == 'exit':  # Quitte le jeu si 'exit' est entré.
        break
    if letter == 'e':  # Utilise une bombe si 'e' est entré.
        use_bomb(p, m)
    else:
        update_p(letter, p, m)  # Met à jour la position du personnage.
    update_objects(p, objects)  # Met à jour les objets collectés.

    if (p["x"], p["y"]) == exit:  # Vérifie si le personnage a atteint la sortie.
        print("Vous avez atteint la sortie !")
        break  # Termine la boucle si le personnage atteint la sortie.
