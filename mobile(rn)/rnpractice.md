# Les bonnes pratiques pour intégrer une application avec React Native

React Native est devenu un choix privilégié pour le développement d'applications mobiles, notamment grâce à sa capacité à créer des applications cross-platform performantes 
tout en partageant une base de code unique entre iOS et Android. Cependant, 
maîtriser React Native implique bien plus que de simplement coder des composants : il faut aussi adopter de bonnes pratiques pour assurer une architecture propre, 
une bonne gestion des performances et une expérience utilisateur fluide

## Comprendre le contexte des projets React Native

### Gestion des maquettes et synchronisation avec Figma

Dans de nombreux projets, le design est fourni sous forme de maquettes sur **Figma**. 
Il est essentiel de synchroniser ces maquettes avec l'intégration afin d'éviter des écarts visuels ou fonctionnels.

👉 **Bonnes pratiques :**

    * Définir dès le début du projet une architecture de composants qui s'aligne avec la structure des maquettes.
    * Éviter d'intégrer du style en dur et privilégier des fichiers centralisés pour garantir une homogénéité visuelle.

### Architecture et marque blanche

Certains projets, comme les solutions « marque blanche », doivent être capables de s'adapter facilement à différentes identités graphiques (couleurs, logos, labels, etc.).

👉 Bonnes pratiques :

    * Utiliser des tokens de design pour appliquer les couleurs et styles dynamiquement selon la marque utilisée.
    * Séparer la logique métier de la gestion de l'interface pour faciliter la personnalisation

## Maîtriser les fondamentaux de React Native

React Native nous propose une large liste d'éléments natifs à utiliser pour l'intégration des composants et des écrans.

### Les composants de base à connaître

    * **Text** : L'élément principal pour afficher du texte.
    * **View** : Un conteneur qui structure les écrans et les composants, équivalent à `<div>` en HTML.
    * **Pressable** : Permet d'ajouter des interactions utilisateur avec différents états (`onPress`, `onPressIn`, `onPressOut`).
    * **TextInput** : Utilisé pour les champs de saisie, avec une gestion avancée des événements.
    * **Image & ImageBackground** : Pour afficher des images locales ou externes avec différentes options d'ajustement (`resizeMode`, `resizeMethod`).

### Hooks React pour gérer l'état et les effets
La bibliothèque React une fois associée au projet met à disposition plusieurs hooks permettant d'utiliser plusieurs fonctionnalités pour aider à la création des composants.

Même si cela ne semble pas concerner les intégrateurs de prime abord, il est important de les comprendre pour pouvoir cohabiter au mieux avec les éléments qui constituent React Native, 
mais également pour pouvoir en faire usage pour de l'animation, de l'amélioration de performances, et bien d'autres.

Les hooks sont essentiels pour gérer les interactions dynamiques et l'état des composants :

    * **useState** : Pour gérer l'état local d'un composant.
    * **useEffect** : Pour exécuter des actions lors du montage ou des mises à jour du composant.
    * **useMemo** : Pour éviter les recalculs inutiles et améliorer les performances.
    * **useCallback** : Pour mémoriser des fonctions et éviter leur recréation à chaque rendu.

👉 Exemple d'optimisation avec useMemo :

```
const accordionTextStyle = useMemo(() => (
  isOpen ? { color: 'blue' } : { color: 'black' }
), [isOpen]);
```

## Gestion du style

### StyleSheet et Flexbox pour une organisation optimale

React Native utilise un système de styles inspiré du CSS, mais avec une syntaxe camelCase (ex. `backgroundColor` au lieu de background-color). 
Flexbox est au cœur de la disposition des composants, facilitant la gestion des grilles et alignements.

```
const styles = StyleSheet.create({
  container: {
    padding: 16,
    backgroundColor: '#FFF',
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
  }
});
```

💡 **Astuce** : Tous les composants en React Native sont en display: *flex par défaut*. Utilisez `flexDirection`, `alignItems` et `justifyContent` pour aligner vos éléments.

### Utilisation des thèmes et tokens pour une meilleure gestion des couleurs

Pour une gestion propre des styles dans des projets complexes, adoptez des fichiers centralisés pour vos thèmes. Cela permet de :

    * Garantir la cohérence visuelle.
    * Faciliter la personnalisation pour chaque client (dans le cas de marques blanches).

```
const theme = {
  colors: {
    primary: '#1E90FF',
    secondary: '#32CD32',
    background: '#F5F5F5',
  },
};
```

## Architecture

### Un fichier unique par composant

En React Native, chaque composant est défini dans un seul fichier .tsx, contrairement à Angular où les fichiers HTML, CSS et TypeScript sont séparés.

👉 **Structure recommandée** :

```
Component.tsx
       // Les imports
       // La définition des "type"
       // Expression du composant
       // Définition des styles
```

Cela garantit une meilleure lisibilité et une maintenance plus facile.

### Sélection de librairies populaires

    * **@react-native-reanimated** pour des animations fluides et complexes.
    * **@gorhom/bottom-sheet** pour des fenêtres modales élégantes.
    * **react-native-safe-area-context** pour gérer les zones de sécurité des appareils.
    * **Expo** pour intégrer des fonctionnalités comme les gradients, les icônes ou encore les vidéos.

## Conseils pour une application performante

### Optimisation des performances
Nous pouvons penser que l'optimisation des performances d'une application est une tâche uniquement liée aux travaux des développeurs. 
Or, dans l'architecture du code React Native, il y a beaucoup de libertés données aux intégrateurs sur la façon de créer des composants ou d'appliquer du style. 
Si nous n'y prêtons pas attention, même à notre échelle, les performances peuvent être impactées.

Cela dépend des projets et de l'implication technique de l'intégrateur, mais nous pouvons déjà mettre des choses en place pour optimiser les performances :

    * Utilisez `useMemo` et `useCallback` pour éviter les calculs ou re-renders inutiles.
        - Mauvaise pratique : `<View style={[styles.container, { padding: insets }]} />`
        - Bonne pratique :
            ```
            const containerStyle = useMemo(() => [
              styles.container, { padding: insets }
            ], [insets]);
            return <View style={containerStyle} />;
            ```

    * Préférez les composants performants comme FlatList pour le rendu des grandes listes.
        ```
        <FlatList
        data={items}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => <Text>{item.name}</Text>}
        />
        ```

### Debugging efficace

En intégration, il est plutôt utile d'avoir un inspecteur comme sur les navigateurs web pour faire du débogage, afin de cibler le style ou les balises qui causent des problèmes.

En React Native, que cela soit sur émulateur ou sur device physique, la seule option qui s'en rapproche est le "Toggle Element Inspector". 
En réalité, il ne restitue qu'une partie très sommaire des styles appliqués et nous donne un bref aperçu des balises sélectionnées.

À l'usage, on se rend rapidement compte de son inutilité. Quand on code pour la première fois c'est un peu déroutant, mais on finit par s'habituer à la bonne vieille méthode : 
mettre des `backgroundColor` sur les balises pour détecter de quelle manière elles s'agencent et adapter leur style en fonction.

### Compatibilité multi-OS

Même si cela devient moins important au fur et à mesure de l'avancée d'un projet applicatif, il est nécessaire de vérifier régulièrement son intégration sur les différents OS.
À chaque ajout de librairie ou à chaque nouveau composant créé, il peut y avoir des différences parfois notables (cela ne fonctionne pas sur l'un ou l'autre, ou l'agencement est différent).

Alors, testez régulièrement votre application sur iOS et Android pour éviter toutes surprises en production.

## Conclusion

Adopter **les bonnes pratiques React Native** vous permettra de créer des applications robustes, performantes et évolutives. En structurant votre projet proprement, 
en utilisant les hooks intelligemment et en optimisant votre code, vous garantirez une expérience fluide à vos utilisateur·rices.

