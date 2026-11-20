# Rapport d'audit de sécurité — OWASP Juice Shop

**Contexte :** UVSQ, M2 IRS — Audits de sécurité 2026/2027 — TP « Audit d'une application web »
**Application auditée :** OWASP Juice Shop 20.4.0 (instance personnelle, GitHub Codespaces)
**Date de l'audit :** 2026-11-20
**Périmètre :** tests réalisés exclusivement sur l'instance personnelle de l'auditeur.

## Synthèse managériale
Trois vulnérabilités, dont deux critiques, exploitables sans outil spécialisé :
1. Tout client connecté peut consulter ET modifier le panier des autres (IDOR/BOLA).
2. Connexion administrateur sans mot de passe via injection SQL.
3. Lien piégé exécutant du code dans le navigateur d'une victime (XSS).
Niveau de risque global : CRITIQUE.

## Phase 2 — Preuves
- IDOR/BOLA (CWE-639) : GET /rest/basket/6 (token B) -> 200, article de A visible ; PUT /api/BasketItems/9 (token B) -> 200, quantite de A modifiee 3->2. Defi "View Basket" resolu.
- Injection SQL (CWE-89) : POST /rest/user/login email=' OR 1=1-- -> 200, session admin@juice-sh.op sans mot de passe ; controle sans injection -> 401. Defi "Login Admin" resolu.
- XSS DOM (CWE-79) : /#/search?q=<iframe src="javascript:alert(xss)"> -> iframe injecte, alert execute. Defi "DOM XSS" resolu.

## Phase 3 — Corrections
- basketItems.ts : filtrer l'article par BasketId de l'utilisateur authentifie (findOne where id + BasketId).
- login.ts : requete parametree (replacements) au lieu de la concatenation.
- search-result.component.ts : supprimer bypassSecurityTrustHtml, afficher en texte ({{ }}) ou DOMPurify ; ajouter une CSP.

## Phase 4 — Criticite
| Faille | CWE | Criticite | CVSS 3.1 |
|---|---|---|---|
| IDOR/BOLA | CWE-639 | Critique | 8.1 |
| Injection SQL | CWE-89 | Critique | 9.8 |
| XSS | CWE-79 | Majeur | 6.1 |

Rapport complet detaille disponible dans le fichier local de l'auditeur.
