# GUIDE DE REVISION - Examen Reduction de Modeles
## Mardi 10 mars 2026, 13h30, 2h, sans documents

---

# PLAN DE REVISION OPTIMISE (8h-00h ce soir + 8h-12h demain)

| Horaire | Bloc | Contenu |
|---------|------|---------|
| 20h-21h30 | **Bloc 1** | Lire ce guide entier une fois (POD/SVD + PGD champ + PGD EDP) |
| 21h30-23h | **Bloc 2** | Reprendre chaque question de l'examen 2025, ecrire les solutions a la main |
| 23h-00h | **Bloc 3** | Quick-fire questions + formules cles a memoriser |
| 8h-10h | **Bloc 4** | Refaire les derivations PGD (Q6, Q7, Q9) sur papier blanc |
| 10h-11h30 | **Bloc 5** | Relire les formules cles, faire les questions flash |
| 11h30-12h | **Bloc 6** | Dernier passage sur les pieges courants |

---

# PARTIE A : POD / SVD (Exercice 1 de l'examen)

## ========================================
## Q1 : Interpretation + demonstration S = XX^T + POD de rang n
## ========================================

### Ce qu'on demande
On a N vecteurs u^1,...,u^N dans R^m (m >> N). X = col(u^i) dans R^{m x N}.
1. Interpreter min_{||phi||=1} sum ||u^i - <u^i,phi>phi||^2
2. Montrer que ca revient a etudier S = XX^T
3. Ecrire le probleme POD de rang n

### Solution complete

**Interpretation :** On cherche la direction unitaire phi qui minimise la somme des erreurs de projection des u^i sur phi. C'est l'axe principal du nuage de points (comme l'ACP).

**Demonstration :**
Par Pythagore : ||u^i||^2 = ||<u^i,phi>phi||^2 + ||u^i - <u^i,phi>phi||^2

Donc minimiser l'erreur = maximiser la projection :

    max_{||phi||=1} sum_{i=1}^{N} (<u^i, phi>)^2

Or <u^i, phi> = (u^i)^T phi. Le vecteur X^T phi dans R^N regroupe tous ces produits scalaires.

    sum (<u^i,phi>)^2 = ||X^T phi||^2 = (X^T phi)^T (X^T phi) = phi^T XX^T phi = phi^T S phi

C'est le **quotient de Rayleigh** : max_{||phi||=1} phi^T S phi

=> La solution est le vecteur propre de S associe a la plus grande valeur propre.

**Proprietes de S = XX^T :**
- Dimension : m x m
- Symetrique : S^T = (XX^T)^T = XX^T = S
- Semi-definie positive : v^T S v = v^T XX^T v = ||X^T v||^2 >= 0

**POD de rang n :** On cherche la base orthonormee Phi = [phi_1,...,phi_n] dans R^{m x n} :

    min_{Phi^T Phi = I_n} sum_{i=1}^{N} ||u^i - Phi Phi^T u^i||^2

=> Les phi_k sont les n vecteurs propres de S associes aux n plus grandes valeurs propres.

### Pieges
- Ne pas oublier ||phi|| = 1 (contrainte unitaire)
- S est m x m, PAS N x N
- L'equivalence min erreur <=> max projection passe par Pythagore

---

## ========================================
## Q2 : Theoreme SVD + SVD tronquee
## ========================================

### Solution

**Theoreme SVD :** Toute matrice X dans R^{m x N} admet la factorisation :

    X = U Sigma V^T

ou :
- U dans R^{m x m} orthogonale (U^T U = I_m) : vecteurs singuliers a gauche
- V dans R^{N x N} orthogonale (V^T V = I_N) : vecteurs singuliers a droite
- Sigma dans R^{m x N} pseudo-diagonale : valeurs singulieres sigma_1 >= sigma_2 >= ... >= 0

**SVD tronquee d'ordre n :**

    X_n = U_n Sigma_n V_n^T

On ne garde que les n plus grandes valeurs singulieres. C'est la **meilleure approximation de rang n** au sens de la norme de Frobenius (theoreme d'Eckart-Young).

### A retenir
- U : m x m (grand), V : N x N (petit)
- Sigma est rectangulaire m x N, pas carree
- Eckart-Young : ||X - X_n||_F = sqrt(sum_{k=n+1}^r sigma_k^2)

---

## ========================================
## Q3 : Lien POD = SVD
## ========================================

### Solution

On injecte la SVD X = U Sigma V^T dans S :

    S = XX^T = (U Sigma V^T)(V Sigma^T U^T) = U (Sigma Sigma^T) U^T = U Lambda U^T

Car V^T V = I_N, donc V disparait.

**Conclusions :**
1. Les colonnes de U (vecteurs singuliers gauches) = vecteurs propres de S (modes POD)
2. Lambda = Sigma Sigma^T, donc **lambda_i = sigma_i^2**
3. Les valeurs propres de S sont les carres des valeurs singulieres

**Calcul pratique (comment calculer la SVD via S) :**
1. Former S = XX^T
2. Diagonaliser S => obtenir U et Lambda
3. sigma_i = sqrt(lambda_i)
4. v_i = (1/sigma_i) X^T u_i (reconstituer V colonne par colonne)

### Piege
- La question dit "la demonstration n'est pas demandee" pour 2025, mais la correction la donne quand meme. Connais-la au cas ou.

---

## ========================================
## Q4 : Utiliser svd() pour approximation a variables separees
## ========================================

### Solution

Soit u(t,x) defini sur [0,T] x [0,L], discretise en N instants et m points.

1. **Construire la matrice des snapshots** X dans R^{m x N} avec X_{i,j} = u(x_i, t_j)
2. **Calculer** [U, S, V] = svd(X)
3. **Approximation separee :**

        u(x,t) ≈ sum_{k=1}^{n} sigma_k U_k(x) V_k(t)

   Chaque mode = sigma_k * (vecteur spatial) * (vecteur temporel)

4. **Choix du nombre de modes n :** critere d'energie

        1 - (sum_{k=1}^{n} sigma_k^2) / (sum_{k=1}^{N} sigma_k^2) <= epsilon

   On prend le plus petit n tel que cette erreur relative <= epsilon.

### A retenir
- Les colonnes de U sont les fonctions spatiales
- Les colonnes de V sont les fonctions temporelles
- sigma_k pondere chaque mode
- L'erreur se calcule directement avec les valeurs singulieres, sans reconstruire la matrice

---

## ========================================
## Q5 : n-epaisseur de Kolmogorov (algorithme pragmatique)
## ========================================

### Definition formelle

    d_n(U, V) = inf_{V_n subset V, dim(V_n)=n} sup_{u in U} inf_{v in V_n} ||u - v||_V

C'est la pire erreur d'approximation quand on prend le meilleur sous-espace de dimension n.

### Algorithme pragmatique

1. **Echantillonnage :** Generer M solutions discretes => matrice des snapshots X
2. **SVD :** Calculer X = U Sigma V^T
3. **Estimation :** d_n(X) = ||X - X_n||_2 = sigma_{n+1}

La (n+1)-ieme valeur singuliere estime la n-epaisseur de Kolmogorov.

### Piege
- C'est sigma_{n+1} (pas sigma_n) car c'est l'erreur APRES avoir garde n modes
- C'est une approximation empirique (depend de l'echantillonnage)

---

# PARTIE B : PGD pour un champ donne (Exercice 2 de l'examen)

## ========================================
## Q6 : Construction de l'approximation PGD d'une fonction u(x,t)
## ========================================

### Ce qu'on demande
Construire u_n(x,t) = sum_{l=1}^{n} Lambda_l(x) lambda_l(t) par algorithme glouton.

### Solution complete

**Principe glouton :** A chaque etape, on connait u_{n-1}. On cherche R(x)S(t) minimisant :

    J(R,S) = (1/2) int_I int_Omega (e(x,t) - R(x)S(t))^2 dx dt

ou e(x,t) = u(x,t) - u_{n-1}(x,t) est le residu.

**Resolution par directions alternees :**

**Etape 1 : S(t) fixe, trouver R(x)**

On prend la variation par rapport a R (perturbation R*) :

    int_I int_Omega (e - RS) R* S dx dt = 0  pour tout R*

On factorise R* et on sort les integrales temporelles :

    int_Omega R* [ int_I e S dt - R int_I S^2 dt ] dx = 0  pour tout R*

Par le lemme fondamental du calcul des variations :

    **R(x) = int_I (u - u_{n-1}) S dt / int_I S^2 dt**

**Etape 2 : R(x) fixe, trouver S(t)**

Par symetrie :

    **S(t) = int_Omega (u - u_{n-1}) R dx / int_Omega R^2 dx**

**Algorithme complet :**
1. Calculer le residu Delta_U = u - u_{n-1}
2. Initialiser S(t) (ex: S = t)
3. Tant que stagnation > seuil :
   - Calculer R(x) avec la formule ci-dessus, normaliser R
   - Calculer S(t) avec la formule ci-dessus
   - Critere de stagnation : s = int(S_new - S_old)^2 dt / int(S_old)^2 dt
4. Stocker (R, S) comme nouveau mode

**Controle de qualite :** Erreur = ||u - u_n||_U / ||u||_U (norme L^2 espace-temps)

### Formules a memoriser (CRITIQUE)

    R(x) = int_I Delta_U * S dt / int_I S^2 dt
    S(t) = int_Omega Delta_U * R dx / int_Omega R^2 dx

### Pieges
- Normaliser R apres chaque calcul (convention : normaliser le spatial)
- Le denominateur est un SCALAIRE (norme au carre de la fonction fixee)
- Ne pas oublier l'orthogonalisation de Gram-Schmidt apres chaque mode

---

## ========================================
## Q7 : PGD pour le probleme de Poisson parametre -Delta u = f(x,t)
## ========================================

### Ce qu'on demande
1. Formulation variationnelle
2. Algorithme glouton (systeme d'equations)
3. Comment accroitre la convergence

### Solution complete

**Formulation variationnelle :**
Multiplier par u* (nulle sur bord Omega), integrer sur Omega x I, Green :

    int_I int_Omega grad(u) . grad(u*) dx dt = int_I int_Omega f u* dx dt

**Algorithme glouton PGD :**
On pose u = u_{n-1} + R(x)S(t). Fonction test : u* = R*(x)S(t) + R(x)S*(t).

On injecte dans la formulation variationnelle :

    int_I int_Omega grad(RS) . grad(R*S + RS*) dx dt = int_I int_Omega (f u* - grad(u_{n-1}).grad(u*)) dx dt

**Etape spatiale (S fixe, test u* = R*S) :**

    (int_I S^2 dt) int_Omega grad(R).grad(R*) dx = int_Omega R* (int_I f S dt) dx - int_Omega grad(R*).(int_I grad(u_{n-1}) S dt) dx

C'est un **probleme de Poisson** pour R(x) ! Forme faible standard avec :
- Rigidite ponderee par int_I S^2 dt (scalaire)
- Second membre integre en temps

**Etape parametrique (R fixe, test u* = RS*) :**

    S(t) (int_Omega |grad(R)|^2 dx) = int_Omega f R dx - int_Omega grad(u_{n-1}).grad(R) dx

C'est une **equation algebrique** pour S(t) ! (pas une ODE ici car pas de derivee temporelle dans l'equation de Poisson)

**Accroitre la convergence (UPDATE) :**
L'algorithme glouton fixe les modes passes => sous-optimal.
Pour accelerer : apres avoir calcule {Lambda_1,...,Lambda_n}, on FIGE la base spatiale et on recalcule SIMULTANEMENT toutes les fonctions temporelles lambda_1(t),...,lambda_n(t) en resolvant un systeme couple de taille n x n.

### Pieges
- Pour Poisson : l'etape temporelle donne une equation ALGEBRIQUE (pas d'ODE)
- Pour l'equation de la chaleur (Q9) : l'etape temporelle donne une ODE
- L'update est une projection de Galerkin globale

---

## ========================================
## Q8 : Conditions aux limites non nulles
## ========================================

### Solution

PGD cherche u = sum Lambda_l(x) lambda_l(t). Si u = g != 0 sur le bord, il faudrait une infinite de modes pour reproduire g exactement avec un produit tensoriel.

**Traitement par relevement :**
1. Definir u_g(x,t) qui satisfait les CL (ex: interpolation lineaire entre les bords)
2. Poser u = u_tilde + u_g
3. u_tilde verifie des CL homogenes (u_tilde = 0 sur le bord)
4. L'equation modifiee : -Delta u_tilde = f + Delta u_g

Maintenant u_tilde est compatible avec la PGD (on impose Lambda_l = 0 sur le bord).

### A retenir
- Relevement = changement de variable
- Modifie le second membre de l'equation
- Pour la chaleur : Q_0 = F - M u_cl_dot - K u_cl

---

# PARTIE C : PGD pour une EDP parabolique (Exercice 3 de l'examen)

## ========================================
## Q9 : PGD pour dtt(u) + dx(u) + u = r(x,t)
## ========================================

### Ce qu'on demande
Detailler la strategie PGD temps-espace. Discuter du cout de calcul.

### Solution complete

**L'equation :** dtt(u) + dx(u) + u = r(x,t) sur [0,1] x [0,T], CL et CI nulles.

**Etape 1 : Formulation variationnelle**
On cherche u(x,t) ≈ X(x)T(t). Fonction test u* = X*T + XT*.

    int_0^T int_0^1 (X T'' + X' T + X T)(X*T + XT*) dx dt = int_0^T int_0^1 r(X*T + XT*) dx dt

**Etape 2 : Sous-probleme spatial (T connu, test X*T)**

On integre les composantes temporelles :

    alpha = int_0^T (T''T + T^2) dt
    beta = int_0^T T^2 dt  (ATTENTION: verifier les termes)

En fait, detaillons. Le terme X T'' teste par X*T donne : int_0^T T''T dt * int_0^1 X X* dx
Le terme X'T teste par X*T donne : int_0^T T^2 dt * int_0^1 X' X* dx
Le terme XT teste par X*T donne : int_0^T T^2 dt * int_0^1 X X* dx

Donc le probleme spatial est :

    int_0^1 (alpha_1 X + beta X' + beta X) X* dx = int_0^1 r_tilde X* dx

avec alpha_1 = int_0^T T''T dt, beta = int_0^T T^2 dt, r_tilde(x) = int_0^T r(x,t)T dt

=> **EDO spatiale 1D**

**Etape 3 : Sous-probleme temporel (X connu, test XT*)**

On integre les composantes spatiales :

    A = int_0^1 X^2 dx
    B = int_0^1 (X'X + X^2) dx
    r_hat(t) = int_0^1 r(x,t) X dx

Le probleme temporel est :

    int_0^T (A T'' + B T) T* dt = int_0^T r_hat T* dt

=> **EDO temporelle 1D** : A T'' + B T = r_hat(t)

**Discussion du cout de calcul :**
- **Approche classique** : Matrice (Nx*Nt)^2 => cout O((Nx*Nt)^p) (MULTIPLICATIF)
- **PGD** : A chaque iteration, on resout separement Nx x Nx (espace) puis Nt x Nt (temps) => cout O(Nx^p + Nt^p) (ADDITIF)

La PGD **contourne la malediction de la dimensionnalite** : gains monumentaux en memoire et temps CPU.

### Pieges
- Bien identifier chaque terme de l'equation (ici dtt, dx, u) et les separer correctement
- Les constantes alpha, beta, A, B dependent des integrales de l'AUTRE variable
- L'equation temporelle est une ODE d'ORDRE 2 (a cause de dtt)
- Le cout est ADDITIF (pas multiplicatif) : c'est l'argument cle

---

# PARTIE D : PGD pour l'equation de la chaleur (le TP2 - tres probable a l'examen)

## ========================================
## Schema complet pour rho*c*du/dt - k*d2u/dx2 = f(x,t)
## ========================================

### Formulation variationnelle

    int_I int_Omega (rho*c * du/dt * u* + k * grad(u).grad(u*)) dx dt = int_I int_Omega f u* dx dt

### Forme semi-discrete (FEM spatial)

    M u_dot + K u = F

avec M = matrice de masse, K = matrice de rigidite

### Changement de variable (si CL non nulles)
u = u_CL + W, avec u_CL satisfaisant les CL.

    M W_dot + K W = Q_0 = F - M u_CL_dot - K u_CL

### PGD : W = sum Lambda_i(x) lambda_i(t)

**Sous-probleme spatial (lambda fixe) :**

    J * Lambda = q

    J = [int_0^T lambda * lambda_dot dt] M + [int_0^T lambda^2 dt] K
    q = int_0^T Q(t) lambda dt

C'est un systeme FE de taille Nx x Nx. Apres resolution, normaliser Lambda.

**Sous-probleme temporel (Lambda fixe) :**

    alpha * lambda_dot + beta * lambda = gamma(t)

    alpha = Lambda^T M Lambda   (scalaire)
    beta = Lambda^T K Lambda    (scalaire)
    gamma(t) = Lambda^T Q(t)    (fonction du temps)

C'est une ODE scalaire ! Resolution par Euler implicite :

    lambda_i = (gamma(t_i)*dt + alpha*lambda_{i-1}) / (alpha + beta*dt)

### Critere de stagnation du point fixe

    s = (lambda_new - lambda_old)^T I_t (lambda_new - lambda_old)^T / (lambda_old^T I_t lambda_old^T)

s < 10^{-5} => convergence

---

# FORMULES CLES A MEMORISER (fiche de derniere minute)

## POD / SVD
```
S = XX^T                         (matrice de covariance)
S = U Lambda U^T                 (diagonalisation = POD)
X = U Sigma V^T                  (SVD)
lambda_i = sigma_i^2             (lien POD-SVD)
v_i = (1/sigma_i) X^T u_i       (reconstituer V)
Erreur Frobenius = sqrt(sum_{k=n+1} sigma_k^2) / sqrt(sum sigma_k^2)
d_n ≈ sigma_{n+1}               (Kolmogorov)
```

## PGD champ
```
R(x) = int_I Delta_U * S dt / int_I S^2 dt
S(t) = int_Omega Delta_U * R dx / int_Omega R^2 dx
```

## PGD equation de la chaleur
```
Spatial:  J*Lambda = q
  J = [lambda*Dt*It*lambda'] M + [lambda*It*lambda'] K
  q = lambda * It * Q'

Temporel: alpha*lambda_dot + beta*lambda = gamma
  alpha = Lambda'*M*Lambda
  beta  = Lambda'*K*Lambda
  gamma = Lambda'*Q

Euler implicite: lambda_i = (gamma_i*dt + alpha*lambda_{i-1})/(alpha + beta*dt)
```

## PGD Poisson -Delta u = f
```
Spatial: (int_I S^2 dt) int_Omega grad(R).grad(R*) dx = ...  [Poisson standard]
Temporel: S(t) * int_Omega |grad(R)|^2 dx = int_Omega fR dx - int_Omega grad(u_{n-1}).grad(R) dx  [algebrique!]
```

## Relevement CL non nulles
```
u = u_tilde + u_g
-Delta u_tilde = f + Delta u_g  (Poisson)
M W_dot + K W = F - M u_CL_dot - K u_CL  (chaleur)
```

## Divers
```
Masse elementaire: m = dx * [1/3  1/6; 1/6  1/3]     (ou dt/6 * [2 1; 1 2])
Rigidite elementaire: k = (1/dx) * [1 -1; -1 1]
Gram-Schmidt: Lambda_{m+1} = Lambda^new_{m+1} - sum <Lambda^new, Lambda_i> Lambda_i
Stagnation: s = ||lambda_new - lambda_old||^2 / ||lambda_old||^2
```

---

# QUESTIONS FLASH (a savoir repondre en 30 secondes)

**1.** Quelle est la dimension de S = XX^T si X est m x N ?
> m x m

**2.** Pourquoi S est semi-definie positive ?
> v^T S v = ||X^T v||^2 >= 0

**3.** Quel est le lien entre valeurs propres de S et valeurs singulieres de X ?
> lambda_i = sigma_i^2

**4.** Comment estimer la n-epaisseur de Kolmogorov ?
> sigma_{n+1} (la premiere valeur singuliere tronquee)

**5.** Dans la PGD d'un champ, quel est le denominateur de la formule pour R(x) ?
> int_I S^2 dt (norme L^2 en temps de S, un scalaire)

**6.** Pourquoi des CL non nulles posent probleme pour la PGD ?
> Il faudrait une infinite de modes pour reproduire g != 0 par produit tensoriel. Solution : relevement.

**7.** Pour l'equation de Poisson -Delta u = f, l'etape temporelle de la PGD donne quoi ?
> Une equation ALGEBRIQUE (pas d'ODE car pas de derivee temporelle)

**8.** Pour l'equation de la chaleur du/dt - Delta u = f, l'etape temporelle donne quoi ?
> Une ODE : alpha lambda_dot + beta lambda = gamma(t)

**9.** Quel est l'avantage en cout de la PGD par rapport a l'approche monolithique ?
> Cout ADDITIF O(Nx^p + Nt^p) au lieu de MULTIPLICATIF O((Nx*Nt)^p)

**10.** Qu'est-ce que l'update dans l'algorithme glouton PGD ?
> On fige la base spatiale et on recalcule simultanement TOUTES les fonctions temporelles (projection de Galerkin globale, systeme couple n x n)

**11.** Comment normaliser Lambda dans la PGD ?
> Lambda <- Lambda / sqrt(Lambda^T I_x Lambda)

**12.** Quel est le critere pour choisir n modes en SVD ?
> 1 - (sum_{k=1}^n sigma_k^2)/(sum sigma_k^2) <= epsilon

**13.** Dans la PGD pour la chaleur, qu'est-ce que J (matrice du probleme spatial) ?
> J = [int lambda*lambda_dot dt] M + [int lambda^2 dt] K

**14.** Comment calculer l'erreur relative en norme L^2(Omega x I) ?
> Integrer d'abord en temps (obtenir vecteur Nx), puis en espace (obtenir scalaire)

**15.** Pour dtt(u) + dx(u) + u = r, l'etape temporelle de la PGD donne quoi ?
> Une ODE d'ORDRE 2 : A T'' + B T = r_hat(t)

---

# ERREURS CLASSIQUES A EVITER

1. **Confondre S = XX^T (m x m) avec X^T X (N x N)** : les deux partagent les memes valeurs propres non nulles, mais S donne les modes POD (vecteurs spatiaux)

2. **Oublier de normaliser Lambda** apres le sous-probleme spatial

3. **Confondre l'etape temporelle** : pour Poisson c'est algebrique, pour la chaleur c'est une ODE d'ordre 1, pour dtt c'est une ODE d'ordre 2

4. **Oublier le relevement** quand les CL sont non nulles

5. **Confondre le residu** : Q = Q_0 - M*W_dot - K*W (pas Q = F)

6. **Ne pas ecrire la fonction test separee** : u* = R*S + RS* (les DEUX termes)

7. **Oublier que le denominateur des formules PGD est un scalaire** (pas une fonction)

8. **Kolmogorov** : c'est sigma_{n+1}, pas sigma_n

9. **Cout PGD** : ADDITIF (pas multiplicatif). C'est le point cle.

10. **Gram-Schmidt** : quand on retire de l'info de Lambda_{m+1}, il faut la compenser dans Gamma_i

---

# STRATEGIE D'EXAMEN

1. **Lire tout le sujet d'abord** (5 min). L'examen 2025 avait 3 exercices, difficulte croissante.

2. **Exercice 1 (POD/SVD)** : le plus classique, points "gratuits". Bien rediger la demo du quotient de Rayleigh. ~30-40 min.

3. **Exercice 2 (PGD champ/Poisson)** : derivations par directions alternees. Ecrire proprement les variations, factoriser, appliquer le lemme du calcul des variations. ~40 min.

4. **Exercice 3 (PGD EDP)** : le plus dur mais structure identique. La cle : bien separer les termes, calculer les constantes temporelles/spatiales. ~40 min.

5. **En cas de blocage** : ecrire la formulation variationnelle + la forme separee de u et u*. Le reste suit mecaniquement.

---

Bonne chance pour demain !
