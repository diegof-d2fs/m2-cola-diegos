# Cola do apresentador — Trabalho 2 (não divulgar)

Contém o **roteiro do apresentador (cola)** do Trabalho 2 de Segurança de Sistemas Computacionais, renderizado via GitHub Pages.

> ⚠️ **Não divulgue a URL.** O plano Free não serve Pages a partir de repositório privado, então este repositório é público com `noindex` + `robots.txt` (fora de buscas e sem links apontando para ele). A cola não deve ser entregue ao professor. O material público oficial (slides + relatório + modelo) está em `m2-seg-system-diegos`.

## Acesso (GitHub Pages)

- **Cola (roteiro):** página inicial do Pages deste repositório.
- **Apresentação (ensaio):** `slides.html`.

Botões da cola: *Ativar rota curta (30 min)*, *Imprimir* e *Abrir apresentação*. Âncoras `#slide-N` abrem direto no roteiro de cada slide.

## Estrutura

| Caminho | O que é |
| --- | --- |
| `index.html` | A cola (roteiro do apresentador) — página inicial |
| `slides.html` | A apresentação, para ensaio |
| `content/cola.md` | Texto do roteiro (fonte da cola) |
| `css/`, `js/`, `assets/` | Mesmo motor estático do deck público |

Editar o roteiro = editar `content/cola.md` e dar `git push`. Sem build; `.nojekyll` garante que o `.md` seja servido como texto.

## Rodar localmente

```sh
python -m http.server 8000
# abra http://localhost:8000/
```
