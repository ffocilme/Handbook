# Teoría de Números

## Computar números primos


### Criba de Erastotenes
**Idea:** marcar múltiplos de cada primo hasta $n$.  
**Complejidad:** $O(n \log \log n)$ 

```cpp
vi primes(int n)
{
    vb isPrime(n + 1, true);
    vi p;
    forn(i, 2, n+1)
    {
        if (isPrime[i])
        {
            p.push_back(i);
            for(ll j = i*i; j <= n; j += i)
                isPrime[j] = true;
        }
    }
    return p;
}
```

$\clearpage$

### Criba Lineal

**Idea:** Existe una optimización que permite realizar la criba en $O(n)$ a cambio de usar más memoria, que solo debe ser usada si $N \leq 10^7$
**Complejidad** : $O(n)$

```cpp
vi linearPrimes(int n)
{
    vi mp(n+1); // Minimum Prime Factor
    vi p;

    forn(i,2,n+1)
    {
        if (mp[i] == 0)
        {
            mp[i] = i;
            p.push_back(i);
        }

        for (int j = 0; i * p[j] <= n; ++j)
        {
            mp[i * p[j]] = p[j];
            if (p[j] == mp[i]) break;
        }
    }
    return p;
}
```

### Contar número de primos en un rango

**Idea:** Realizar una suma prefijos
**Complejidad de precomputo:** $O(n \log \log \sqrt{n})$
**Complejidad de consulta:** $O(1)$

```cpp
vi countPrimes(int n, vb &is_prime)
{
    vi ps(n+1, 0);
    forn(i,2,n+1)
    {
        if (is_prime[i]) ps[i] = ps[i-1] + 1;
        else ps[i] = ps[i-1];
    }
    return ps;
}

int countPrimes(int l, int r, vi &count)
{
    return ((l == 1) ? count[r] :  count[r] - count[l-1]);
}
```

$\clearpage$

### Encontrar primos en un rango

Encontrar primos en un rango

Encontrar números primos en un rango $[L, R]$ donde $R - L + 1 \approx 10^7$ y $R \leq 10^{12}$.

```cpp
vb segmentedSieveNoPreGen(long long vbL, long long r) {
    vb isPrime(r - l + 1, true);
    ll lim = sqrt(r);
    for (ll i = 2; i <= lim; ++i)
        for (ll j = max(i * i, (l + i - 1) / i * i); j <= r; j += i)
            isPrime[j - l] = false;
            
    if (l == 1) isPrime[0] = false;
    return isPrime;
}

```

## Números Primos y Factores

**Definición:**  
Un número $a$ es **factor** (o **divisor**) de $b$ si $a$ divide a $b$, denotado $a \mid b$.  
Un entero $n > 1$ es **primo** si sus únicos divisores positivos son $1$ y $n$.

**Factorización prima única:**  
Todo entero $n>1$ se puede escribir de forma única (hasta orden) como:
$$
n = p_1^{\alpha_1} p_2^{\alpha_2} \cdots p_k^{\alpha_k}
$$

$\clearpage$

### Cantidad de factores

Sea $\tau(n)$ el número de factores de un entero n. Por ejemplo, $\tau(12) = 6$, porque los factores de 12 son 1, 2, 3, 4, 6 y 12. Para calcular el valor de $\tau(n)$, podemos usar la fórmula

$$
\tau(n) = \prod_{i=1}^{k} (\alpha_i + 1)
$$

ya que para cada primo $p_i$, hay $\alpha_i + 1)$ formas de elegir cuántas veces aparece en el factor. Por ejemplo, como $12 = 2^2 \cdot 3^1$ $\tau(12) = (2+1) \cdot 1(2) = 6$.

**Complejidad:** $O(\sqrt{n})$
```cpp
int tau(n)
{
    int ans = 0;
    for (int i=2; i*i<=n; i++)
    {
        int cnt = 0;
        while (n \% i == 0) { n/=i; cnt++;}
        ans *= (cnt+1);
    }
    if (n > 1) ans *= 2;
    return ans;
}
```

$\clearpage$

## Suma de factores
Sea $\sigma(n)$ la suma de los factores de un entero n. Por ejemplo, $\sigma(12) = 28$, porque (1 + 2 + 3 + 4 + 6 + 12 = 28). Para calcular el valor de $\sigma(n)$, podemos usar la fórmula

$$
\sigma(n) = \prod_{i=1}^{k} (1 + p_i + \dots + p_i^{\alpha_i}) = \prod_{i=1}^{k} \frac{p_i^{\alpha_i+1} - 1}{p_i - 1}
$$

donde la última forma se basa en la fórmula de progresión geométrica. 
Por ejemplo, $ \sigma(12) = \frac{2^3 - 1}{2 - 1} \cdot \frac{3^2 - 1}{3 - 1} = 28$

**Complejidad:** $O(\sqrt{n})$
```cpp
ll sigma(n)
{
    ll ans = 0;
    for (int i=2; i*i<=n; i++)
    {
        ll cnt = 0, p = 1;
        while (n \% i == 0) { n/=i; cnt++; p *= i;}
        ans *= (cnt+1);

        if(cnt > 0)
            ans *= (p*i-1) / (i-1);
    }
    if (n > 1) ans *= (1LL * n * n - 1) / (i-1);
    return ans;
}
```

$\clearpage$

### Lista de factores

Observa que cada factor primo aparece en el vector tantas veces como divide el número.

Por ejemplo $12 = 2^2 * 3$, por lo que el resultado de la función es $[2,2,3]$

**Complejidad:** $O(\sqrt{n})$
```cpp
vi factors(int n) {
    vi f;
    for (int x = 2; x*x <= n; x++) 
    {
        while (n \% x == 0) {f.push_back(x); n /= x;}
    }
    if (n > 1) f.push_back(n);
    return f;
} 
```

Variante que toma la criba y devuelve un map donde el valor de cada clave es la potencia, ejemplo
$12 = \{2:2, 3:1\}$

**Complejidad:** $O(\pi(\sqrt{n}) + \log n) \approx O(\frac{\sqrt{n}}{\log n} + \log n)$
```cpp
map<ll,ll> factors(ll n, vi primes)
{
    map<ll,ll> f;
    for (auto d : primes)
    {
        if (d*d > n) break;
        while (n \%d == 0) {f[d]++; n /= d;}
    }
    if (n > 1) f[n]++;
    return f;
}
```
## Pruebas de primalidad

Revisar si un número es primo.

```cpp
bool isPrime(int n)
{
    if (n < 2) return false;
    for (int x = 2; x*x <=n; x++)
        if (n\%x == 0) return false;
    return true;
}
```

### Algoritmos probabilisticos

Estos algorimtos se basan en comprobar de manera probabilistica basado en el Teoerema debil de Fermat, cuanto mayor sea la cantidad de iteraciones, mneor es la probabilidad de equviocarse.

#### Prueba de Fermat
La prueba de Fermat puede fallar con los números de Charmichael  ($a^{n-1} \equiv 1 \bmod n$ se cumple para todo$a$ coprimo con $n$, por ejemplo $561 = 3 \times 11 \times 17$), sin embargo en la practica se sigue usando ya que es bastante rápida y los números de Charmichael son raros.

```cpp
bool isPrimeFermat(int n, int iter=5) {
 if (n < 4) return n == 2 || n == 3;
 for (int i = 0; i < iter; i++)
 {
     int a = 2 + rand() \% (n - 3);
     if (binpow(a, n - 1, n) != 1) return false;
 }
 return true;
}
```

#### Prueba de Miller

Miller desmostro que el algoritmo se puede volver determinista si se prueban todas bases $a \leq 2 ln(n)^2$,
siendo que para los números de 64 bit, basta con revisar las primeras 12 bases primas.


```cpp
using u64 = uint64_t;
using u128 = __uint128_t;
bool check_composite(u64 n, u64 a, u64 d, int s) {
    u64 x = binpower(a, d, n);
    if (x == 1 || x == n - 1) return false;
    for (int r = 1; r < s; r++) 
    {
        x = (u128)x * x % n;
        if (x == n - 1) return false;
    }
    return true;
}
bool MillerRabin(u64 n)
{
    if (n < 2) return false;
    int r = 0;
    u64 d = n - 1;
    while ((d & 1) == 0) { d >>= 1;r++; }
    for (int a : {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37}) 
    {
        if (n == a) return true;
        if (check_composite(n, a, d, r)) return false;
    }
    return true;
}
```

$\clearpage$

### Primos como diferencia de cuadrados


Un numero impar se puede escribir $n = pq$ como la diferencia de cuadrados $n = a^2 - b^2$.

$$n = (\frac{p+q}{2})^2 - (\frac{p-q}{2})^2$$

Estos valores $p$ y $q$ se pueden obtener con el metodo de factorización de Fermat. $O(|p-q|)$ cabe
mencionar que si los números son demasiado grande o la distancia entre $p$ y $q$, este algoritmo es
extremadamente lento.

```cpp
pair<int,int> fermat(int n) {
    int a = ceil(sqrt(n));
    int b2 = a*a - n;
    int b = round(sqrt(b2));
    while (b * b != b2) 
    {
        a = a + 1;
        b2 = a*a - n;
        b = round(sqrt(b2));
    } 
    return {a-b,n/(a-b)};
}
```

## Algoritmo de Euclides


### Máximo Comúm Divisior

$$
\gcd(a,b) = \gcd(b, a \bmod b)
$$

**Complejidad:** $O(\log \min(a,b))$.

```c
int gcd(int a, int b) 
{
    return b == 0 ? a : gcd(b, a)% b;
}
```
### Máximo Común Multiplo

$$
\mathrm{lcm}(a,b) = \frac{a \cdot b}{\gcd(a,b)}
$$

**Complejidad:** $O(\log \min(a,b))$.

```c
int lcm(int a, int b) 
{
    return a / gcd(a,b) * b;
}
```

$\clearpage$
### Euclides Extendido

**Devuelve** $x,y$ tales que $a x + b y = \gcd(a,b)$ — útil para inversos modulares y diofánticas.

```cpp
int egcd(int a, int b, int *x, int *y) 
{
    if (b == 0) { *x = 1; *y = 0; return a; }
    int x1, y1;
    int g = egcd(b, a % b, &x1, &y1);
    *x = y1;
    *y = x1 - (a / b) * y1;
    return g;
}
```

### Numeros Coprimos

Dos enteros $a$ y $b$ son coprimos si $\gcd(a,b) = 1$.

```cpp
bool coprimes(int a, int b)
{
    return gcd(a,b) == 1;
}
```

### Funcion Totiente de Euler

La **función totiente de Euler** $\varphi(n)$ da el número de enteros entre $1$ y $n$ que son coprimos con $n$ si 
$$
n = p_1^{\alpha_1} \cdots p_k^{\alpha_k},
$$

$$
\varphi(n) = \prod_{i=1}^{k} p_i^{\alpha_i - 1} (p_i - 1)
$$

**Ejemplo:** $10 = 2 \cdot 5 \;\Rightarrow\; \varphi(10) = 2^{1-1}(2-1) \cdot 5^{1-1}(5-1) = 4$

**Complejidad:** $O(\sqrt{n})$
```cpp
ll phi(ll n) {
    ll result = n;
    for (ll p = 2; p * p <= n; ++p) {
        if (n % p == 0) {
            while (n % p == 0) n /= p;
            result -= result / p;
        }
    }
    if (n > 1) result -= result / n;
    return result;
}
```

$\clearpage$

### Teorema de Euler 

**Teorema de Euler:**

$$
x^{\varphi(m)} \equiv 1 \pmod{m}, \quad x \text{ coprimo con } m
$$

Si $m$ es primo ($\varphi(m) = m - 1$):

### Pequeño Teorema de Fermat
$$
x^{m - 1} \equiv 1 \pmod{m}
$$


## Inversos Multiplicativos Modulares

El inverso multiplicativo de $x$ módulo $m$, $\mathrm{inv}_m(x)$, cumple:

$$
x \cdot \mathrm{inv}_m(x) \equiv 1 \pmod{m}
$$

Existe si y solo si $\gcd(x, m) = 1$, y se puede calcular con:

$$
\mathrm{inv}_m(x) = x^{\varphi(m) - 1} \pmod{m}
$$

Si $m$ es primo:

$$
\mathrm{inv}_m(x) = x^{m - 2} \pmod{m}
$$

**Ejemplo:** $\mathrm{inv}_{17}(6) = 6^{15} \bmod 17 = 3$


```cpp
u64 binpow(u64 a, u64 b) {
    u64 r = 1;
    a %= MOD;
    while (b > 0) {
        if (b & 1) r = MULTIPLY(r, a);
        a = MULTIPLY(a, a);
        b >>= 1;
    }
    return r;
}
u64 modInverse(u64 a) {return binpow(a, MOD - 2);}
```

$\clearpage$

## Ecuaciones Diofánticas

Una ecuación diofántica tiene la forma:

$$
a x + b y = c, \quad a, b, c \in \mathbb{Z}
$$

Se puede resolver si $c$ es divisible por $\gcd(a,b)$.  
Si $(x_0, y_0)$ es una solución, todas las soluciones son:

$$
\begin{cases}
x = x_0 + k \dfrac{b}{\gcd(a,b)} \\\\
y = y_0 - k \dfrac{a}{\gcd(a,b)} \\\\
k \in \mathbb{Z}
\end{cases}
$$

**Ejemplo:** resolver $39x + 15y = 12$ $\gcd(39, 15) = 3 \mid 12$

Usando el algoritmo de Euclides extendido:

$39 \cdot 2 + 15 \cdot (-5) = 3 \implies 39 \cdot 8 + 15 \cdot (-20) = 12$

```cpp
pair<bool, pair<ll,ll>> solve_diophantine(ll a, ll b, ll c) {
    ll x0, y0;
    ll g = ext_gcd(llabs(a), llabs(b), x0, y0);
    if (c % g != 0) return {false, {0,0}};
    if (a < 0) x0 = -x0;
    if (b < 0) y0 = -y0;
    ll mult = c / g;
    x0 *= mult;
    y0 *= mult;
    return {true, {x0, y0}};
}
```

$\clearpage$

### Teorema Chino del Resto

**Sistema de congruencias:**

$$
\begin{cases}
x \equiv a_1 \pmod{m_1} \\\\
x \equiv a_2 \pmod{m_2} \\\\
\vdots \\\\
x \equiv a_n \pmod{m_n}
\end{cases}
$$

con $m_i$ coprimos dos a dos.

**Solución general:**

$$
x = \sum_{k=1}^{n} a_k X_k \, \mathrm{inv}_{m_k}(X_k)
$$

donde

$$
X_k = \frac{m_1 m_2 \cdots m_n}{m_k}
$$

**Ejemplo:**

$$
\begin{cases}
x \equiv 3 \pmod{5} \\\\
x \equiv 4 \pmod{7} \\\\
x \equiv 2 \pmod{3}
\end{cases}
\implies
x = 263
$$

**Infinitas soluciones:**

$$
x + m_1 m_2 \cdots m_n
$$

```cpp
struct Congruence{
    ll a, m;
};

ll chinese_remainder_theorem(vector<Congruence> & cgrs)
{
    ll m = 1, ans = 0, ai, mi, ni;
    for (auto &c : cgrs) m *= c.m;

    for (auto const& c : cgrs) {
        ai = c.a;
        mi = m / c.m;
        Ni = mod_inv(mi, c.m);
        ans = (ans + ai * mi \% m * ni) \% m;
    }
    return ans;
}
```

## MEX

El **MEX** ("minimum excludant") de un conjunto de números enteros es el **mínimo número entero no presente en el conjunto**.  

Ejemplo: $\mathrm{mex}(\{0,1,3\}) = 2$

```cpp
int mex(vi &v)
{
    set<int> s(all(v));
    int m = 0;
    while (s.count(m)) m++;
    return m;
}
```
# Juegos NIM

- Hay \(n\) heaps con \(x_i\) palillos.
- Jugadores alternan turnos.
- En cada turno se remueven palillos de un heap no vacío.
- Gana quien remueve el último palillo.

## Nim Sum

$$
s = x_1 \oplus x_2 \oplus \dots \oplus x_n
$$

- Si $\(s = 0\)$ estado perdedor.
- Si $\(s \neq 0\)$ estado ganador.

## Movimiento óptimo

Para un estado ganador:

- Elegir heap $\(i\)$ tal que $\(x_i \oplus s < x_i\)$
- Remover hasta que el heap tenga $\(x_i \oplus s\)$ palillos.


## Teorema de Sprague–Grundy

El Teorema de Sprague–Grundy generaliza la estrategia de Nim a cualquier juego que cumpla:

- Dos jugadores alternan turnos.
- El juego consiste en estados, y los movimientos posibles dependen solo del estado.
- El juego termina cuando un jugador no puede moverse.
- El juego termina seguro eventualmente.
- Los jugadores tienen información completa y no hay aleatoriedad.

$\clearpage$

## Números de Grundy

Cada estado del juego se asocia con un **número de Grundy** \(G(s)\), equivalente al tamaño de un heap de Nim.

Si $\(s\)$ tiene movimientos hacia los estados $\(s_1, s_2, \dots, s_n\)$:

$$
\text{mex}\(\{g_1,g_2,...,g_n\}\)
$$
