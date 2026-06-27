# Meilleur XI de la J3 — Coupe du Monde 2026 (Squad Selector)

Widget interactif (1 fichier HTML par langue, sans dépendance hors `html2canvas` CDN).
Le lecteur choisit un **schéma tactique** (4-3-3 / 4-4-2 / 4-2-3-1 / 4-1-4-1), compose son
**XI** depuis un pool multi-pays (37 joueurs), aperçu **live** sur le terrain, puis **partage**
(X, Facebook, WhatsApp, Instagram) ou **télécharge l'image**.

Photos joueurs : résolues via l'**API SportMonks** (`/v3/football/players/search/{nom}` →
`image_path`) puis figées en URLs CDN dans le fichier (aucun token exposé, aucune dépendance
réseau à l'API au chargement).

## Fichiers (1 par langue)

| Langue | Fichier | `lang` |
|---|---|---|
| Français | `xi-j3-cdm26.html` | `fr` |
| English | `xi-j3-cdm26-en.html` | `en` |
| Português (BR) | `xi-j3-cdm26-pt-br.html` | `pt-BR` |
| Español | `xi-j3-cdm26-es.html` | `es` |

Seuls le bloc `CONFIG` (COUNTRY, I18N, country labels) et les libellés d'interface changent
entre les langues : le moteur JS est identique.

## Intégration WordPress (bloc « HTML personnalisé »)

1. Héberge les 4 fichiers (GitHub Pages, comme les autres widgets : `jcrochet-netizen.github.io/<repo>/…`).
2. Sur chaque page localisée, colle le bloc ci-dessous (remplace `<REPO>` et le `src` par le bon fichier).

```html
<!-- ① Contenu crawlable AUTOUR de l'iframe (le contenu d'une iframe n'est PAS
     attribué à la page par Google → il faut du vrai texte indexable ici). -->
<h2>Compose le meilleur XI de la 3ᵉ journée de la Coupe du Monde 2026</h2>
<p>Choisis ton schéma tactique et aligne tes 11 titulaires parmi les joueurs les plus
en vue après la J3 : Maignan, Mbappé, Haaland, Messi, Vinícius Júnior, Musiala, Salah,
Bruno Guimarães… Compose ta compo et partage-la.</p>

<!-- ② Le widget -->
<iframe
  id="wc26-totw-xi"
  src="https://jcrochet-netizen.github.io/<REPO>/xi-j3-cdm26.html"
  title="Compose le meilleur XI de la J3 de la Coupe du Monde 2026"
  loading="lazy"
  scrolling="no"
  referrerpolicy="strict-origin-when-cross-origin"
  style="width:100%;max-width:820px;display:block;margin:0 auto;border:0;overflow:hidden;min-height:900px;"></iframe>

<script>
(function () {
  var ORIGIN = 'https://jcrochet-netizen.github.io';      // origine de confiance (hébergeur du widget)
  var frame  = document.getElementById('wc26-totw-xi');
  window.addEventListener('message', function (e) {
    if (e.origin !== ORIGIN) return;                      // 1) n'accepte QUE les messages du widget
    var d = e.data; if (!d || !d.type) return;
    if (d.type === 'wc2026-resize' || d.type === 'xilegende-resize') {   // auto-resize (anti-CLS, pas de double-scroll)
      var h = parseInt(d.height, 10);
      if (h && h > 0 && frame) frame.style.height = h + 'px';
    }
    if ((d.type === 'wc2026-getUrl' || d.type === 'xilegende-getUrl') && e.source) {
      e.source.postMessage({ type: 'wc2026-url', url: location.href }, ORIGIN);  // URL réelle → partages précis
    }
  }, false);
})();
</script>
```

### Bonnes pratiques SEO appliquées
- **Texte crawlable autour de l'iframe** (H2 + intro + noms de joueurs) : le contenu de l'iframe
  appartient au domaine `github.io` et n'est **pas** attribué à la page WordPress. Sans texte
  autour, la page est « vide » pour Google.
- **Widget en `noindex, nofollow`** (déjà dans chaque fichier) → pas d'indexation concurrente /
  duplicate du contenu de l'iframe.
- **`referrerpolicy="strict-origin-when-cross-origin"`** + réponse `wc2026-url` : le widget
  utilise l'URL exacte de la page pour les partages (sinon repli sur l'URL courante).
- **Contrôle `e.origin`** : seul notre widget peut redimensionner l'iframe ; le message ne
  transporte qu'un entier.
- **`loading="lazy"`** (perf / LCP), **`scrolling="no"` + `overflow:hidden`** (pas de
  double-scroll mobile), **`min-height`** de repli (anti-CLS), largeur centrée `max-width:820px`.
- **1 widget par page localisée** + `lang` correct dans chaque fichier → cohérent avec une
  stratégie **hreflang** (relie les 4 pages entre elles).

### Par langue — remplace seulement `src` + `title`
| Langue | `src` (fichier) | `title` conseillé |
|---|---|---|
| FR | `xi-j3-cdm26.html` | Compose le meilleur XI de la J3 de la Coupe du Monde 2026 |
| EN | `xi-j3-cdm26-en.html` | Build the best XI of Matchday 3 of the 2026 World Cup |
| PT-BR | `xi-j3-cdm26-pt-br.html` | Monte o melhor XI da 3ª rodada da Copa do Mundo 2026 |
| ES | `xi-j3-cdm26-es.html` | Crea el mejor XI de la J3 del Mundial 2026 |

---
*Outil éditorial non officiel — joueurs de la J3, Coupe du Monde 2026.*
