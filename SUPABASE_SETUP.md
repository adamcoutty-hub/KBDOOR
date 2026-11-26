# Connexion Supabase

Le site sait enregistrer les commandes (`commandes`) et demandes de devis/contact (`demandes_contact`) dans Supabase. Il utilise la clé publique `anon` côté navigateur et des règles RLS qui autorisent uniquement les insertions anonymes. Le stockage local du navigateur reste disponible comme solution de secours.

## Mise en service

1. Créez un projet Supabase et ouvrez **SQL Editor**.
2. Exécutez le script [`supabase/schema.sql`](supabase/schema.sql).
3. Exécutez ensuite [`supabase/tracking.sql`](supabase/tracking.sql). Cette migration crée la table `suivi_commandes` pour conserver l'historique des statuts et ajoute les fonctions sécurisées de suivi/gestion.
4. Dans **Project Settings > API**, copiez l'URL du projet et la clé `anon` / publishable.
5. Renseignez ces valeurs dans [`supabase-config.js`](supabase-config.js) :

   ```js
   window.KBDOOR_SUPABASE_CONFIG = {
     url: 'https://VOTRE-PROJET.supabase.co',
     anonKey: 'VOTRE_CLE_PUBLIQUE_ANON'
   };
   ```

6. Dans **Authentication > Users**, créez ou sélectionnez le compte administrateur, puis modifiez ses **app_metadata** en ajoutant `"role": "admin"`. N'accordez pas ce rôle aux comptes clients.
7. Déployez les fichiers du dépôt. Les clients peuvent suivre leur commande avec son numéro et le téléphone associé ; l'administrateur se connecte à [`admin-commandes.html`](admin-commandes.html) pour mettre à jour le statut.

**Important :** ne mettez jamais la clé `service_role` dans `supabase-config.js` ni dans le navigateur. Les clés `anon` sont publiques : les politiques RLS limitent les accès à l'insertion, mais une protection anti-spam (par exemple CAPTCHA ou Edge Function avec limitation de débit) reste recommandée avant une mise en production à grande échelle.

La page de gestion ne permet pas l'inscription. Seuls les comptes dont le champ `app_metadata.role` vaut `admin` peuvent consulter les commandes et modifier leurs statuts. La recherche publique de suivi ne renvoie que le numéro, la date et le statut, après vérification du numéro de commande et du téléphone.

La fonction de suivi publique devrait être protégée contre les recherches automatisées (par exemple par une Edge Function avec limitation de débit) avant une mise en production à grande échelle.
