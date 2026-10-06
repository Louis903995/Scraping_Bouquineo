# Bouquineo – collecteur de fiches produit

Scrape books.toscrape.com (liste + 1000 fiches) puis charge dans PostgreSQL.

## Lancement depuis zéro
```bash
docker compose up -d          # PostgreSQL (crée le schéma)
uv run scraper.py --limit 20  # échantillon ; sans --limit = les 1000
uv run load.py                # relançable sans doublon
```

## Choix
- **Clé = UPC** : le titre n'est pas unique. Rechargement = upsert.
- **Note** : lue dans la classe CSS `star-rating Three` -> 3.
- **Stock réel** : uniquement sur la fiche (`In stock (22 available)`).
- **Reprise** : `data/books.jsonl` est écrit fiche par fiche ; au démarrage, les URL déjà présentes sont sautées.
- **Rythme** : 0,5 s entre requêtes + User-Agent explicite ; arrêt après 10 échecs consécutifs.
- **Prix/taxe** : à compléter après comparaison sur ~10 livres (HT vs TTC vs taxe).

## Question centrale
```sql
SELECT title, stock, rating FROM books WHERE stock <= 5 ORDER BY stock, rating DESC;
SELECT title, rating, stock FROM books ORDER BY rating DESC, reviews DESC LIMIT 20;
```
