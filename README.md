# 📖 Pokédex amb Python + API

Benvinguts! Aquest projecte és un **Jupyter Notebook** que es connecta a internet per obtenir informació dels primers 150 Pokémon i mostrar-los en una graella visual. Ho fa utilitzant una **API pública** (PokéAPI), sense necessitat de registrar-se ni tenir cap clau secreta.

> 💡 **Què és una API?** És com una finestreta d'un restaurant: tu demanes (petició) i el cuiner (servidor) et torna el que has demanat (resposta en format JSON).

---

## 👀 Com es veu el resultat

Quan executes el Notebook, veuràs:

**1. El logo oficial de la Pokédex a dalt de tot.**

**2. Una graella amb els 50 primers Pokémon, com aquesta:**

```
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│  🖼️ img  │  │  🖼️ img  │  │  🖼️ img  │  │  🖼️ img  │  │  🖼️ img  │
│ bulbasaur│  │  ivysaur │  │ venusaur │  │charmander│  │charmeleon│
└──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘

┌──────────┐  ┌──────────┐  ┌──────────┐  ...
│  🖼️ img  │  │  🖼️ img  │  │  🖼️ img  │
│ charizard│  │ squirtle │  │wartortle │
└──────────┘  └──────────┘  └──────────┘
```

Cada cel·la mostra la **imatge oficial** del Pokémon (artwork d'alta qualitat) i el seu **nom** a sota.

## 🧠 Explicació del codi pas a pas

### 📌 Pas 1 — Importem les llibreries necessàries

```python
import requests
from PIL import Image
from io import BytesIO
from IPython.display import HTML, display
```

Aquí li diem a Python quines eines volem fer servir:

- `requests` → per connectar-nos a internet i demanar dades a servidors externs
- `PIL / Image` → per treballar amb imatges (obrir-les, redimensionar-les, etc.)
- `BytesIO` → per convertir dades binàries (com una imatge descarregada) en un objecte que Python pugui llegir directament
- `HTML` i `display` → per mostrar imatges i codi HTML dins del Jupyter Notebook

---

### 📌 Pas 2 — La funció `showPokemonsInfo()`

```python
def showPokemonsInfo(resposta):
    resposta = requests.get(url)
    if resposta.status_code == 200:
        info = resposta.json()
        print("ID:", info['id'])
        print("Nom:", info["name"])
        print("Alçada:", info["height"])
        print("Pes:", info["weight"])
        imatge_url = info["sprites"]["other"]["official-artwork"]["front_default"]
        display(Image(url=imatge_url, width=150))
        print("-" * 30)
```

Aquesta funció mostra la informació detallada d'un Pokémon concret. Fa el següent:

1. **Fa una petició GET** a la URL indicada (per exemple, la URL d'un Pokémon específic).
2. **Comprova si ha funcionat** mirant el codi de resposta HTTP. El codi `200` significa *"tot correcte"*. Altres codis comuns:
   - `404` → no trobat
   - `500` → error del servidor
3. **Converteix la resposta a JSON** amb `.json()`. El JSON és un format de text estructurat, igual que un diccionari de Python.
4. **Imprimeix** l'ID, el nom, l'alçada i el pes del Pokémon.
5. **Mostra la imatge oficial** a 150 píxels d'amplada.

**Exemple de sortida per pantalla:**
```
ID: 1
Nom: bulbasaur
Alçada: 7
Pes: 69
[imatge de Bulbasaur]
------------------------------
```

**Exemple de JSON que retorna l'API:**
```json
{
  "id": 1,
  "name": "bulbasaur",
  "height": 7,
  "weight": 69,
  "sprites": {
    "other": {
      "official-artwork": {
        "front_default": "https://raw.githubusercontent.com/.../1.png"
      }
    }
  }
}
```

---

### 📌 Pas 3 — Mostrem el logo de la Pokédex

```python
url_imatge = "https://archives.bulbagarden.net/media/upload/4/4b/Pok%C3%A9dex_logo.png"
info = requests.get(url_imatge)
img_logo = Image.open(BytesIO(info.content))

display(HTML(f"""
<div style="text-align:left;">
    <img src="{url_imatge}" width="40%">
</div>
"""))
```

Aquí descarreguem i mostrem el logo:

1. Definim la URL de la imatge del logo (el `%C3%A9` és la lletra `é` codificada per a URLs).
2. Fem una petició GET: `info.content` conté els bytes de la imatge descarregada.
3. Obrim la imatge amb `PIL` mitjançant `BytesIO`, que converteix els bytes en un fitxer llegible.
4. Generem un bloc HTML amb una **f-string** (una cadena de text on pots inserir variables amb `{}`) per mostrar la imatge al 40% d'amplada.

---

### 📌 Pas 4 — Creem el contenidor HTML de la graella

```python
html = """<div style="
    display: grid;
    grid-template-columns: repeat(5, 200px);
    gap: 20px;
    ">
    """
```

Aquí preparem una variable `html` que conté el codi HTML d'un `<div>` amb **CSS Grid**:

- `display: grid` → activa el mode de graella
- `repeat(5, 200px)` → 5 columnes de 200px cadascuna
- `gap: 20px` → 20 píxels d'espai entre cel·les

> **CSS Grid** és una tècnica de disseny web per organitzar elements en files i columnes, molt útil per fer galeries d'imatges.

---

### 📌 Pas 5 — Demanem la llista dels 150 primers Pokémon

```python
url = "https://pokeapi.co/api/v2/pokemon?limit=150"
resposta = requests.get(url)
```

Fem una petició a l'API demanant els primers 150 Pokémon. El paràmetre `?limit=150` és com dir-li a l'API: *"dóna'm com a màxim 150 resultats"*.

La resposta té aquest format:
```json
{
  "results": [
    { "name": "bulbasaur", "url": "https://pokeapi.co/api/v2/pokemon/1/" },
    { "name": "ivysaur",   "url": "https://pokeapi.co/api/v2/pokemon/2/" },
    { "name": "venusaur",  "url": "https://pokeapi.co/api/v2/pokemon/3/" }
  ]
}
```

Fixeu-vos que la llista només conté el **nom** i una **URL** per a cada Pokémon. Hem de fer una petició addicional per a cadascun per obtenir la imatge!

---

### 📌 Pas 6 — Iterem pels 50 primers i construïm la graella

```python
for pokemon in dades["results"][:50]:
    resposta = requests.get(pokemon["url"])

    if resposta.status_code == 200:
        dades = resposta.json()
        imatge = dades["sprites"]["other"]["official-artwork"]["front_default"]

        html += f"""
        <div style="text-align:center;">
            <img src="{imatge}" width="120">
            <p>{dades["name"]}</p>
        </div>
        """
```

Per a cada Pokémon de la llista (els 50 primers, gràcies a `[:50]`):

1. **Fem una nova petició** a la URL específica d'aquell Pokémon per obtenir tots els seus detalls.
2. **Extraiem la URL de la imatge** oficial des del JSON.
3. **Afegim un `<div>`** a la variable `html` amb la imatge (120px) i el nom centrat. L'operador `+=` va afegint-hi nous Pokémon cada volta del bucle.

**Per exemple, per a Bulbasaur s'afegirà:**
```html
<div style="text-align:center;">
    <img src="https://...bulbasaur.png" width="120">
    <p>bulbasaur</p>
</div>
```

---

### 📌 Pas 7 — Tanquem la graella i la mostrem

```python
html += "</div>"
display(HTML(html))
```

Tanquem el `<div>` contenidor i mostrem tot el HTML generat dins del Notebook. En aquest moment és quan apareix la graella visual a la pantalla!

---

## 🔧 Com modificar el codi (experimenta!)

| Vull... | Canvia això |
|---------|-------------|
| Veure més Pokémon | `[:50]` → `[:150]` |
| Imatges més grans | `width="120"` → `width="200"` |
| Més columnes | `repeat(5, 200px)` → `repeat(7, 150px)` |
| Menys columnes | `repeat(5, 200px)` → `repeat(3, 200px)` |
| Veure info detallada | Crida `showPokemonsInfo(url)` amb la URL d'un Pokémon |

---

## 📡 API Utilitzada

- **PokéAPI** → [https://pokeapi.co](https://pokeapi.co)
- És gratuïta, pública i no necessita cap clau d'accés.
- Retorna dades en format **JSON**.

---

## ⚠️ Possibles errors i solucions

| Error | Causa possible | Solució |
|-------|---------------|---------|
| `ModuleNotFoundError` | Llibreria no instal·lada | `pip install requests Pillow` |
| `ConnectionError` | Sense connexió a internet | Comprova el WiFi |
| La graella no es veu | No has executat totes les cel·les | `Cell → Run All` |
| Imatges no carreguen | L'API està momentàniament caiguda | Espera uns minuts i torna-ho a provar |

---. Les imatges i noms dels Pokémon pertanyen als seus propietaris originals (Nintendo / Game Freak / The Pokémon Company).
