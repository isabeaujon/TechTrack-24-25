# Levensverwachting per jaar
Een datavisualisatie waarbij de levensverwachting per land door de jaren heen zichtbaar wordt.

## Waar gaat het over?
In de datavisualisatie worden de verschillen in levensverwachting tussen verschillende landen duidelijk weergegeven. Dit is belangrijk omdat er diverse factoren zijn die van invloed kunnen zijn op de levensverwachting, zoals gezondheidszorg en levensstijl. Deze factoren kunnen zowel een positieve als negatieve invloed hebben op de levensverwachting, afhankelijk van het land. Het is essentieel om te begrijpen dat deze invloeden wereldwijd variëren, wat leidt tot verschillen in de levensverwachting.
Daarnaast wordt in de visualisatie ook de verandering in levensverwachting over de jaren heen zichtbaar. Bij sommige landen is er bijvoorbeeld een groot verschil tussen de jaren.

Op de kaart worden de levensverwachtingen weergegeven als cirkels, waarbij elke cirkel een land representeert. De kleur van de cirkel geeft de hoogte van de levensverwachting aan: landen met een levensverwachting onder de 70 jaar zijn rood, tussen de 70 en 80 jaar geel, en boven de 80 jaar groen. Door op een cirkel te klikken, kun je de exacte levensverwachting voor dat land zien. Met de filter bovenaan kun je de levensverwachting door de jaren heen bekijken.

Als je liever alle landen naast elkaar wilt zien zonder telkens op een land te moeten klikken, kun je de barchart gebruiken. Hier worden de landen naast elkaar weergegeven. Met de slider daaronder kun je door de jaren heen navigeren en het verschil in levensverwachting tussen de jaren zien.

## Gebouwd met:
- Svelte
- SvelteKit
- LeafLet
- Deployment: Vercel



## Creating a project

If you're seeing this, you've probably already done this step. Congrats!

```bash
# create a new project in the current directory
npx sv create

# create a new project in my-app
npx sv create my-app
```

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.
