# SOGECA — Tableau de bord Portefeuille & CA

Version autonome (hors Claude) du tableau de bord SOGECA : portefeuille clients,
sorties, répartition par collaborateur, atterrissage (charges fixes, PCA
théorique et à taux fixe, primes), trésorerie (compte courant, Comptes à
Terme, Excédent PRO), créances clients / dettes fournisseurs étalées,
comparatif N-1 réel, potentiel CWE Pennylane, et prospects.

Ce dossier contient tout le nécessaire pour héberger le site sur Internet,
avec une vraie base de données partagée et un code d'accès.

## Ce qui a changé par rapport à la version Claude

Dans Claude, les données étaient sauvegardées automatiquement par l'outil
(`window.storage`). Cette fonctionnalité n'existe qu'à l'intérieur de Claude.
Cette version utilise à la place **Supabase**, un service de base de données
gratuit et simple à mettre en place, sans écrire de code serveur.

Si aucune base Supabase n'est configurée, le site fonctionne quand même,
mais chaque ordinateur/navigateur garde ses propres données en local.

## Ce qu'il faut prévoir avant de commencer

- Un compte [Supabase](https://supabase.com) (gratuit)
- Un compte [GitHub](https://github.com) (gratuit)
- Un compte [Netlify](https://netlify.com) (gratuit) — recommandé, voir note ci-dessous
- Node.js, uniquement si vous voulez lancer le site en local (optionnel)

**Note sur l'hébergeur** : Vercel et Netlify fonctionnent tous les deux.
Netlify a été testé avec succès pour ce projet — en cas de doute, préférez-le.

---

## Étape 1 — Créer la base de données (Supabase)

1. [supabase.com](https://supabase.com) → **New project** → nommez-le, choisissez un mot de passe (à conserver), une région Europe → **Create new project**
2. **SQL Editor** → **New query** → collez le contenu de [`supabase/schema.sql`](./supabase/schema.sql) → **Run**
   (Ce script peut être rejoué sans risque si vous recommencez : il supprime puis recrée les règles d'accès.)
3. **New query** à nouveau → collez le contenu de [`supabase/seed.sql`](./supabase/seed.sql) (fichier volumineux, c'est normal — il contient les 881 dossiers) → **Run**
4. Vérifiez : **Table Editor** → table `kv_store` → 8 lignes attendues
5. **Project Settings** (⚙️) → **API** → notez **Project URL** et la clé **anon public**

## Étape 2 — Mettre le code sur GitHub

⚠️ **Point critique** : l'upload par glisser-déposer sur GitHub peut aplatir
les dossiers si vous sélectionnez tout en une fois. Procédez en **deux temps** :

1. [github.com](https://github.com) → **+** → **New repository** → nom au choix → **Private** → ne cochez rien → **Create repository**
2. Cliquez sur **uploading an existing file**
3. **Premier glisser-déposer** : sélectionnez uniquement les *dossiers* `src`, `data`, `supabase`, `scripts` (pas leur contenu — les dossiers eux-mêmes) et glissez-les ensemble
4. Attendez la fin de l'upload, puis **second glisser-déposer** : sélectionnez tous les *fichiers isolés* à la racine (`package.json`, `index.html`, `vite.config.js`, `tailwind.config.js`, `postcss.config.js`, `netlify.toml`, `.gitignore`, `.env.example`, `README.md`) et glissez-les
5. **Commit changes**
6. Vérifiez sur la page du dépôt : vous devez voir des dossiers 📁 (`src`, `data`, `supabase`, `scripts`) ET des fichiers isolés au même niveau — jamais un unique dossier qui les contient tous

Ne mettez jamais de fichier `.env` sur GitHub.

## Étape 3 — Déployer sur Netlify

1. [netlify.com](https://netlify.com) → **Sign up** → **Continue with GitHub**
2. **Add new site** → **Import an existing project** → **Deploy with GitHub** → choisissez votre dépôt
3. Netlify détecte `netlify.toml` automatiquement (build command et dossier de publication déjà configurés — c'est le fichier qui a résolu l'écran blanc lors de notre premier essai)
4. Avant de déployer, ajoutez les variables d'environnement (**Site configuration** → **Environment variables**, ou directement sur l'écran d'import) :

| Nom | Valeur |
|---|---|
| `VITE_SUPABASE_URL` | votre Project URL (étape 1) |
| `VITE_SUPABASE_ANON_KEY` | votre clé anon public (étape 1) |
| `VITE_ACCESS_CODE` | le code d'accès de votre choix |
| `VITE_ADVANCED_CODE` | un second code, pour les onglets financiers sensibles (voir « Accès restreint » plus bas) |

5. **Deploy site**

Au bout d'1 à 2 minutes : « Site is live ✨ ». Ouvrez l'adresse `....netlify.app`,
entrez votre code d'accès, vérifiez que le portefeuille s'affiche.

### Alternative : Vercel

Fonctionne aussi, avec la même précaution à l'étape 2 (structure de dossiers).
Ajoutez les mêmes 3 variables d'environnement avant de cliquer sur **Deploy**.

---

## Utilisation au quotidien

- Toute personne qui a l'adresse du site **et** le code d'accès peut l'ouvrir, voir le portefeuille et le modifier — les changements sont partagés instantanément entre tous les utilisateurs
- Pas de comptes séparés : tout le monde a les mêmes droits, pas de traçabilité de qui modifie quoi
- Dernière écriture gagne en cas de modification simultanée
- Pour changer le code d'accès : modifiez `VITE_ACCESS_CODE` dans Netlify/Vercel et redéployez

## Accès restreint (onglets financiers)

Un second code (`VITE_ADVANCED_CODE`) protège spécifiquement les onglets
Atterrissage, Prospects, Primes, Atterrissage + Prospects, Trésorerie,
Créances & Dettes et Comparatif N-1 — ils affichent un cadenas 🔒 dans le
menu tant qu'il n'est pas saisi. Le Tableau de bord, Sorties clients et
Par collaborateur restent accessibles avec le seul code principal.

Une fois saisi, ce second code reste actif pour le reste de la session du
navigateur (comme le code principal) — chaque personne doit le ressaisir
si elle ferme complètement son navigateur.

## Pour aller plus loin

Si vous voulez qu'un développeur reprenne ce projet (comptes utilisateurs,
export Excel automatique, historique des modifications...), ce dossier lui
donne une base de code claire et fonctionnelle.

