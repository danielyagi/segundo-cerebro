# Defuddle - Extração de Conteúdo Web

## Data: 30/07/2026

## O que é

Defuddle é uma biblioteca Node.js para extrair e limpar conteúdo de páginas web (leitura de artigos, remoção de anúncios/navegação, padronização de HTML). Similar ao readability do Mozilla, porém mais moderno.

## Instalação

```bash
npm install defuddle linkedom jsdom
```

## Uso Básico (Node.js)

```javascript
import { JSDOM } from 'jsdom';
import { Defuddle } from 'defuddle/node';

const dom = new JSDOM(html, { url: 'https://example.com/article' });
const result = await Defuddle(dom.window.document, 'https://example.com/article', {
  markdown: true
});

console.log(result.title);
console.log(result.author);
console.log(result.content); // HTML ou Markdown limpo
```

## CLI

```bash
# Parsear uma URL
npx defuddle parse https://example.com/article

# Saída como Markdown
npx defuddle parse page.html --markdown

# Saída como JSON
npx defuddle parse page.html --json

# Extrair propriedade específica
npx defuddle parse page.html --property title
```

## Propriedades retornadas

| Propriedade | Tipo | Descrição |
|---|---|---|
| author | string | Autor do artigo |
| content | string | Conteúdo extraído e limpo |
| title | string | Título do artigo |
| description | string | Descrição/resumo |
| domain | string | Domínio do site |
| published | string | Data de publicação |
| wordCount | number | Total de palavras |
| language | string | Idioma (BCP 47) |
| parseTime | number | Tempo de parsing (ms) |

## Bundles

- `defuddle` (core): browser, sem dependências
- `defuddle/full`: inclui parsing de math e conversão Markdown
- `defuddle/node`: para Node.js, aceita qualquer DOM (jsdom, linkedom, happy-dom)

## Projeto associado

Script criado: `/opt/data/parse_article.js` - exemplo funcional de uso com JSDOM.

## Observações

- Instalado em `/opt/data/.npm-global` devido a restrições de permissão no `/opt/hermes/`
- Requer `"type": "module"` no package.json para usar `defuddle/node`