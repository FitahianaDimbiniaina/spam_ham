Fichiers CSV :

1. erro_spam.csv
   - CSV de base contenant toutes les données brutes des emails.

2. ready_for_analysis.csv
   - CSV nettoyé pour analyse, généré par preClean.ipynb.
   - Contient : Subject, email_body, is_forwarded et éventuellement d’autres colonnes pour l’analyse exploratoire.

3. ready_for_training.csv
   - CSV normalisé pour l’entraînement des modèles, généré par cleaning.ipynb.
   - Contient : texte nettoyé et normalisé (<LINK>, <MONEY>, <DATE>, <NUMBER>), minuscules, is_forwarded (0/1), label.

Notebooks :

1. preClean.ipynb
   - Prépare le CSV pour analyse exploratoire.
   - Actions : séparation Subject / email_body, détection is_forwarded, nettoyage de valeurs manquantes.
   - Résultat : ready_for_analysis.csv
2. analysis.ipynb
   - Analyse exploratoire des données pour identifier des tendances et informations pertinentes.
   - Objectif : comprendre la distribution des emails spam vs ham, la fréquence des mots clés, des liens, des montants, et des emails transférés.
   - Cette étape permet de mieux orienter le nettoyage, la normalisation et le choix des variables pour l’entraînement des modèles.
   - Elle fournit également des insights sur les caractéristiques les plus discriminantes entre spam et ham, ce qui renforce la crédibilité des modèles.

3. cleaning.ipynb
   - Prépare les données pour entraînement.
   - Actions : 
       - Normalisation : liens → <LINK>, montants → <MONEY>, dates → <DATE>, nombres → <NUMBER>
       - Conversion en minuscules
       - Suppression/normalisation de la ponctuation
       - Remplissage des valeurs manquantes
   - Résultat : ready_for_training.csv

4. check.ipynb
   - Entraîne et évalue les modèles.
   - Actions :
       - Vectorisation TF-IDF du texte
       - Ajout de is_forwarded comme feature
       - Entraînement Naive Bayes et Logistic Regression
       - Évaluation performance : accuracy, classification report
       - Prédiction sur nouveaux emails via predict_message()

Processus global :

1. Données brutes → Nettoyage initial (preClean.ipynb)
   - Séparation Subject / email_body
   - Détection is_forwarded
   - Export : ready_for_analysis.csv

2. Nettoyage avancé → Normalisation (cleaning.ipynb)
   - Remplacement <LINK>, <MONEY>, <DATE>, <NUMBER>
   - Conversion en minuscules
   - Suppression ponctuation
   - Remplissage valeurs manquantes
   - Export : ready_for_training.csv

Entraînement des modèles (check.ipynb) :

1. Choix des variables :
   - Texte combiné : on a pris le sujet de l’email (Subject) et le corps normalisé (email_clean) et on les a concaténés pour former la variable text.
   - Feature supplémentaire : is_forwarded (1 si l’email est un transfert, 0 sinon).
   - Cible : label (0 = ham, 1 = spam).

2. Séparation des données :
   - On a séparé le jeu de données en ensemble d'entraînement et ensemble de test (80% train, 20% test) tout en gardant la proportion spam/ham identique dans les deux ensembles.

3. Vectorisation :
   - Le texte est transformé en vecteurs numériques avec TF-IDF, limitant le nombre de mots à 5000 pour ne pas surdimensionner le modèle.
   - Cette transformation permet aux modèles d’interpréter l’importance des mots dans chaque email.

4. Combinaison des variables :
   - La feature is_forwarded est ajoutée aux vecteurs TF-IDF pour que le modèle prenne en compte à la fois le contenu textuel et le statut de transfert.

5. Entraînement des modèles :
   - On a entraîné Naive Bayes et Logistic Regression sur l’ensemble combiné texte + is_forwarded.
   - Les modèles apprennent à distinguer spam et ham en se basant sur le texte normalisé et le statut de transfert.

6. Évaluation et prédiction :
   - Les performances sont testées sur le jeu de test avec des mesures comme l’accuracy et le classification report.
   - Pour prédire de nouveaux emails, la fonction predict_message applique le même prétraitement (remplacement des liens, montants, dates, nombres), vectorise le texte et combine is_forwarded avant de faire la prédiction.

NB : la variable is_forwarded influence fortement le résultat, car dans ce dataset, seuls les emails ham sont des emails partagés. Cela explique pourquoi cette feature est très discriminante pour les modèles.