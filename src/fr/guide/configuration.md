# Configuration

## Vite Plugin Symfony

Afin de conserver un maximum de flexibilité, j'ai décidé de ne pas créer de couche d'abstraction par dessus le fichier de configuration `vite.config.js`.

La seule option requise est `build.rollupOptions.input`. Avec `vite-plugin-symfony` cette option doit être définie en tant qu'objet.

```ts
export type InputOption = {
  // entryAlias sera utilisé par nos fonctions Twig
  [entryAlias: string]: string;
};
```

si vous avez un thème contenant uniquement des règles css (pas de js) il peut être intéressant de définir un point d'entrée avec un fichier \[s\]css. cela permettra notamment d'[empêcher le FOUC](/fr/guide/tips#css-files-as-entrypoint) lors du développement.

```js
// vite.config.js
import {defineConfig} from "vite";
import symfonyPlugin from "vite-plugin-symfony";

export default defineConfig({
    plugins: [
        symfonyPlugin(),
    ],

    build: {
        rollupOptions: {
            input: {
                 /* relatif à l'option root */
                app: "./assets/app.ts",

                /* vous pouvez également fournir des fichiers [s]css */
                theme: "./assets/theme.scss"
            },
        }
    },
});
```

Afin de faciliter l'utilisation de Vite sans configuration, l'extension préconfigure certaines options de Vite si celles-ci n'ont pas encore été définies. (voir [code source](https://github.com/lhapaipai/vite-plugin-symfony/blob/main/src/index.ts))

```ts{4-15}
// vite-plugin-symfony/src/index.ts
config(userConfig) {

  const extraConfig: UserConfig = {
    base: userConfig.base ?? "/build/",
    publicDir: false,
    build: {
      manifest: true,
      outDir: userConfig.build?.outDir ?? "public/build",
    },
    optimizeDeps: {
      // Définir sur true pour anticiper le chargement des dépendances.
      force: true,
    },
  };

  return extraConfig;
}
```

Pour toutes les options disponibles, vous pouvez consulter la page [Configuration : Vite plugin Symfony](/fr/config/vite-plugin-symfony).


## Vite Bundle

Si vous modifiez certaines propriétés dans votre fichier `vite.config.js`, vous devrez probablement créer un fichier de configuration `pentatrion_vite.yaml` pour que votre bundle synchronise ces modifications. ça concerne:

- Options de base de `vite`
  - `base`
  - `build.outdir`


```yaml{3,4}
#config/packages/pentatrion_vite.yaml
pentatrion_vite:
  public_directory: public
  build_directory: build

  # etc...
```

Pour toutes les options disponibles, vous pouvez consulter la page [Configuration : Vite Bundle](/fr/config/vite-bundle).
