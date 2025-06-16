# Les **fondamentaux** et **concepts intermédiaires** de React Native

---

## 🧱 1. Fondamentaux de React Native

### ✅ Qu’est-ce que React Native ?

React Native permet de créer des applications mobiles **iOS et Android** avec **JavaScript** et **React**, en utilisant des composants natifs au lieu du DOM HTML.

---

### 1.1. Composants de base

#### Exemples :

```jsx
import { View, Text, Button } from 'react-native';

export default function App() {
  return (
    <View style={{ padding: 20 }}>
      <Text>Hello React Native!</Text>
      <Button title="Click Me" onPress={() => alert('Clicked')} />
    </View>
  );
}
```

**Composants importants :**

* `View`: conteneur, comme `<div>`
* `Text`: pour afficher du texte
* `Image`: pour les images
* `ScrollView`: pour le défilement
* `TextInput`: champ de saisie utilisateur

---

### 1.2. Styles

React Native utilise une API de styles semblable à CSS mais via JavaScript.

```jsx
const styles = {
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center'
  }
};
```

---

### 1.3. Layout avec Flexbox

Identique à CSS Flexbox, mais orienté colonne par défaut.

```jsx
<View style={{ flex: 1, flexDirection: 'row' }}>
  <View style={{ flex: 1, backgroundColor: 'red' }} />
  <View style={{ flex: 1, backgroundColor: 'blue' }} />
</View>
```

---

### 1.4. État et événements

Utilisation de `useState` pour la gestion locale.

```jsx
const [count, setCount] = useState(0);

<Button title="+" onPress={() => setCount(count + 1)} />
```

---

### 1.5. Navigation

Utilisation de **React Navigation** (lib externe)

```bash
npm install @react-navigation/native
```

Exemple avec `Stack.Navigator` :

```jsx
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

<NavigationContainer>
  <Stack.Navigator>
    <Stack.Screen name="Home" component={HomeScreen} />
    <Stack.Screen name="Details" component={DetailsScreen} />
  </Stack.Navigator>
</NavigationContainer>
```

---

## 🔁 2. Concepts intermédiaires

---

### 2.1. API natives et accès aux capteurs

Via des modules comme `react-native-device-info`, `expo-location`, `react-native-camera`, etc.

```jsx
import { useEffect } from 'react';
import * as Location from 'expo-location';

useEffect(() => {
  (async () => {
    let { status } = await Location.requestForegroundPermissionsAsync();
    if (status === 'granted') {
      let location = await Location.getCurrentPositionAsync({});
      console.log(location);
    }
  })();
}, []);
```

---

### 2.2. Gestion de l’état global

Utilisation de **Context API** ou **Redux**

```jsx
const ThemeContext = createContext();

<ThemeContext.Provider value="dark">
  <MyComponent />
</ThemeContext.Provider>
```

---

### 2.3. Accès réseau

Utilisation de `fetch` ou `axios`.

```jsx
fetch('https://api.example.com/users')
  .then(res => res.json())
  .then(data => setUsers(data));
```

---

### 2.4. Stockage local

#### AsyncStorage

```bash
npm install @react-native-async-storage/async-storage
```

```jsx
await AsyncStorage.setItem('token', '123abc');
const token = await AsyncStorage.getItem('token');
```

---

### 2.5. Gestion des permissions

Avec `PermissionsAndroid` (Android) ou `expo-permissions` (Expo)

```jsx
const granted = await PermissionsAndroid.request(
  PermissionsAndroid.PERMISSIONS.CAMERA
);
```

---

### 2.6. Appels natifs (Bridge)

Créer des modules natifs en Swift/Kotlin pour étendre React Native.
→ Requiert niveau avancé natif.

---

### 2.7. Animations

#### Utilisation de `Animated` :

```jsx
const fadeAnim = useRef(new Animated.Value(0)).current;

useEffect(() => {
  Animated.timing(fadeAnim, {
    toValue: 1,
    duration: 2000,
    useNativeDriver: true
  }).start();
}, []);
```

---

### 2.8. Gestion des erreurs

Utilisation de `try/catch`, `ErrorBoundary`, et monitoring avec des services comme Sentry.

---

### 2.9. Test & Debug

* Debug avec Flipper, console, ou React Dev Tools.
* Tests unitaires avec **Jest**.
* Tests E2E avec **Detox**.

---

## 🛠️ 3. Outils recommandés

* **Expo** : pour démarrer rapidement sans config native
* **React Native CLI** : pour projets personnalisés natifs
* **TypeScript** : fortement recommandé pour robustesse
* **ESLint + Prettier** : pour le linting/code style

---

## 📦 Exemple de structure de projet

```
/App.js
/components/
  Button.js
/screens/
  Home.js
  Details.js
/navigation/
  StackNavigator.js
/services/
  api.js
```

---
