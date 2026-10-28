# SOTVP Gestion — version avec base de données

Cette version est une application web responsive (PWA-ready) qui peut être installée sur l’écran d’accueil d’un téléphone. Elle utilise Supabase Auth + PostgreSQL avec Row Level Security (RLS) pour le stockage durable et l’accès réservé aux membres de SOTVP.

## Fonctions incluses dans ce prototype
- Connexion par e-mail et mot de passe.
- Bons de commande partagés entre les membres SOTVP.
- Dépenses associées à un BC ou générales, avec payeur Mourad/Khalid.
- Statuts de BC, historique et synthèse.
- Tables séparées pour mouvements de caisse, prêtes à être reliées à l’écran caisse dans une prochaine itération.

## Installation / activation

### 1. Créer le projet Supabase
1. Créez un projet sur Supabase.
2. Dans **SQL Editor**, exécutez le contenu de `supabase/schema.sql`.
3. Dans **Project Settings → API**, copiez l’URL du projet et la clé publique `anon` (publishable key selon l’interface).
4. Copiez `.env.example` en `.env` et renseignez les deux valeurs. Ne placez jamais une clé `service_role`/secret dans le frontend.

### 2. Créer les comptes
1. Lancez l’application et créez votre compte avec votre e-mail et un mot de passe fort.
2. Faites de même pour Khalid.
3. Dans Supabase → Authentication → Users, copiez les UUID de chaque utilisateur.
4. Dans Table Editor, ouvrez `organizations`, copiez l’UUID de la ligne `SOTVP`.
5. Dans SQL Editor, remplacez les trois valeurs ci-dessous par les UUID réels et exécutez :

```sql
insert into public.organization_members (organization_id,user_id,role)
values
('UUID_ORGANISATION_SOTVP','UUID_MOURAD','owner'),
('UUID_ORGANISATION_SOTVP','UUID_KHALID','member')
on conflict (organization_id,user_id) do nothing;
```

Les deux comptes verront les mêmes BC et dépenses. Les règles RLS bloquent l’accès des comptes non membres.

### 3. Lancer localement pour tester
Installez Node.js sur un ordinateur, puis dans le dossier du projet :

```bash
npm install
npm run dev
```

Pour construire la version de production :

```bash
npm run build
```

Le dossier `dist` peut être hébergé sur un service statique HTTPS (par exemple Netlify, Vercel ou Cloudflare Pages). Configurez les variables `VITE_SUPABASE_URL` et `VITE_SUPABASE_ANON_KEY` dans les paramètres de build de l’hébergeur.

### 4. Installer sur téléphone
Une fois hébergée en HTTPS, ouvrez l’adresse dans Chrome sur Android puis choisissez **Ajouter à l’écran d’accueil**. Pour une vraie application Play Store, une étape supplémentaire d’emballage et de publication est nécessaire.

## Sécurité et limites à connaître
- RLS protège les données au niveau de la base; gardez-la activée.
- N’ajoutez jamais la clé secrète `service_role` au code client.
- Utilisez des mots de passe différents et activez la confirmation e-mail.
- Le code de départ ne comprend pas encore l’envoi de factures/photos, export Excel/PDF, gestion de TVA, recettes encaissées, édition complète, sauvegarde automatique externe, ni gestion avancée des mouvements de caisse. Ces éléments doivent être ajoutés avant de l’utiliser comme comptabilité officielle.
- Le total « reste après dépenses » dans l’interface est indicatif et ne constitue pas le bénéfice comptable.
- Le code fourni est un point de départ technique. La mise en ligne et la création des comptes/projet Supabase doivent être faites avec les identifiants du propriétaire de la société.
