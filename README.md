# Esteios

**Notas para a minha filha** — Um livro filosófico estruturado em torno da Paideia e da tradição estóica.

## Sobre o Projecto

Este é um livro vivo, escrito ao longo do tempo, organizado em três círculos concêntricos:

- **Círculo I — Logos** (O Eu Interior)
- **Círculo II — Ethos** (Com os Outros)
- **Círculo III — Cosmos** (No Mundo)

Inspirado nas *Meditações* de Marco Aurélio, no *Encheirídion* de Epicteto, e na *Paideia* de Werner Jaeger.

## Estrutura Técnica

```
/chapters       → Capítulos finalizados (15 no total)
/fragments      → Notas em bruto, rascunhos, sementes
/sources        → Referências, citações, notas de leitura
/styles         → Design minimalista
/data           → Plano filosófico e técnico original
```

## Estado Atual

**O site está atualmente não publicado.** Apenas uma página placeholder com a palavra "Paciência" está visível em:
**https://bmhash.github.io/esteios/**

Os capítulos estão escritos mas não publicados. Ficheiros em `.quartoignore` não são incluídos no deploy.

## Workflow de Escrita

### Para escrever (sem publicar)

```bash
# Editar capítulos
code chapters/01-nao-es-o-que-te-acontece.qmd

# Preview local (apenas tu vês)
quarto preview

# Commit local (não publica)
git add .
git commit -m "draft: capítulo X"
git push
```

**Importante:** Fazer `git push` **não publica automaticamente**. O deploy é manual.

### Para publicar o livro completo

Quando estiveres pronto para publicar o livro:

1. **Remove o `.quartoignore`:**
   ```bash
   rm .quartoignore
   ```

2. **Muda o projeto de website para book** em `_quarto.yml`:
   ```yaml
   project:
     type: book
     output-dir: _book
   
   book:
     title: "Esteios"
     subtitle: "Notas para a minha filha"
     author: "bmhash"
     date: today
     date-format: "D MMMM YYYY"
     language: pt
   
     chapters:
       - index.qmd
       - part: "Círculo I — O Eu Interior (Logos)"
         chapters:
           - chapters/01-nao-es-o-que-te-acontece.qmd
           - chapters/02-a-opiniao-que-formas.qmd
           - chapters/04-sobre-o-que-e-verdadeiramente-bom.qmd
           - chapters/10-a-admiracao-e-uma-forma-de-inteligencia.qmd
       - part: "Círculo II — Com os Outros (Ethos)"
         chapters:
           - chapters/03-a-coragem-nao-e-a-ausencia-do-medo.qmd
           - chapters/05-como-tratar-um-estranho.qmd
           - chapters/06-suficiente.qmd
           - chapters/07-sobre-o-amor-e-as-suas-exigencias.qmd
           - chapters/11-sobre-o-corpo.qmd
           - chapters/12-o-que-deves-a-beleza.qmd
           - chapters/13-a-comunidade-a-que-pertences.qmd
       - part: "Círculo III — No Mundo (Cosmos)"
         chapters:
           - chapters/08-sobre-o-fracasso.qmd
           - chapters/09-vais-morrer-vive-agora.qmd
           - chapters/14-sobre-a-paciencia.qmd
           - chapters/15-uma-carta-sem-palavras.qmd
   
   format:
     html:
       theme: 
         - litera
         - styles/typography.scss
       toc: true
       toc-depth: 2
       number-sections: false
       lang: pt
   ```

3. **Restaura o `index.qmd`** com a introdução original (guardada em Git history)

4. **Atualiza o workflow** em `.github/workflows/quarto-publish.yml`:
   ```yaml
   - name: Upload artifact
     uses: actions/upload-pages-artifact@v3
     with:
       path: _book  # muda de _site para _book
   ```

5. **Commit e publica manualmente:**
   ```bash
   git add .
   git commit -m "feat: publish full book"
   git push
   
   # Trigger manual deployment
   gh workflow run "Publish Quarto Book to GitHub Pages"
   ```

### Para publicar apenas a página placeholder

```bash
# Trigger manual deployment (estado atual)
gh workflow run "Publish Quarto Book to GitHub Pages"
```

Ou via GitHub web interface:
1. Vai a **Actions** → **Publish Quarto Book to GitHub Pages**
2. Clica **Run workflow** → **Run workflow**

## Comandos Locais

```bash
# Preview local
quarto preview

# Render apenas index (estado atual)
quarto render index.qmd --to html

# Render livro completo (quando configurado como book)
quarto render --to html

# Render PDF (requer TinyTeX)
quarto install tinytex
quarto render --to pdf
```

## Princípios de Design

- **Longevidade acima da tendência** — HTML simples, portável
- **Escrita em primeiro lugar** — Markdown puro, sem fricções
- **Portabilidade** — HTML, PDF, ePub da mesma fonte
- **Tipografia sóbria** — Crimson Text/Palatino, leitura confortável
- **Privacidade** — Sem rastreamento, sem comentários, sem partilha social
- **Controlo total** — Deploy manual, nunca automático

---

*«A posse que ninguém pode tirar ao homem é a paideia.»* — Menandro
