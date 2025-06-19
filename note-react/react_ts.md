# Notions clés 

Voici un panorama des notions clés à maîtriser quand on développe des applications React avec TypeScript, accompagné d’explications et d’exemples concrets.

---

## 1. Configuration de base (tsconfig.json)

Avant tout, il faut configurer TypeScript pour React. Un fichier `tsconfig.json` minimal pourrait ressembler à :

```jsonc
{
  "compilerOptions": {
    "target": "ES6",
    "module": "ESNext",
    "jsx": "react-jsx",        // pour React 17+
    "strict": true,            // active toutes les vérifications strictes
    "esModuleInterop": true,   // interop CommonJS/ESModule
    "skipLibCheck": true,      // ignore le typage des dépendances
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

* **`jsx: "react-jsx"`** : utilise le nouveau transform JSX de React 17+.
* **`strict: true`** : regroupe toute une série d’options strictes (null‑checks, inférence, etc.).
* **`esModuleInterop`** évite les erreurs lors de l’import de modules CommonJS.

---

## 2. Typage des composants et des props

### Fonctionnel vs Classique

* **Fonctionnel** (préféré aujourd’hui) :

  ```tsx
  type ButtonProps = {
    label: string;
    onClick?: () => void;
  };

  const Button: React.FC<ButtonProps> = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
  );
  ```

* **Avec React.FC** :

  * Avantages : typage implicite de `children`.
  * Inconvénients : plus verbeux et `children` toujours présent.

* **Sans React.FC** (plus explicite) :

  ```tsx
  function Button({ label, onClick }: ButtonProps) {
    return <button onClick={onClick}>{label}</button>;
  }
  ```

### Props optionnelles et valeurs par défaut

```tsx
type GreetingProps = {
  name: string;
  age?: number;          // optionnelle
};

const Greeting: React.FC<GreetingProps> = ({ name, age = 18 }) => (
  <p>Bonjour {name}, {age} ans.</p>
);
```

---

## 3. Typage du state et des hooks

### useState

TypeScript infère normalement le type :

```tsx
const [count, setCount] = useState(0);    // count: number
```

Pour des états complexes :

```tsx
type User = { id: string; name: string };

const [user, setUser] = useState<User | null>(null);
```

### useReducer

Pour gérer des logiques plus avancées :

```tsx
type State = { todos: string[] };
type Action =
  | { type: "add"; payload: string }
  | { type: "remove"; payload: number };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "add":
      return { todos: [...state.todos, action.payload] };
    case "remove":
      return {
        todos: state.todos.filter((_, i) => i !== action.payload),
      };
    default:
      return state;
  }
}

const [state, dispatch] = useReducer(reducer, { todos: [] });
```

---

## 4. Typage du Context API

```tsx
interface AuthContextType {
  user: User | null;
  login: (username: string) => void;
}

const AuthContext = React.createContext<AuthContextType | undefined>(undefined);

export const AuthProvider: React.FC = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);

  const login = (username: string) => setUser({ id: "1", name: username });

  return (
    <AuthContext.Provider value={{ user, login }}>
      {children}
    </AuthContext.Provider>
  );
};

export function useAuth(): AuthContextType {
  const ctx = React.useContext(AuthContext);
  if (!ctx) throw new Error("useAuth must be used within AuthProvider");
  return ctx;
}
```

> **Astuce** : toujours vérifier que le contexte n’est pas `undefined` pour éviter les accès hors provider.

---

## 5. Références et forwardRef

Pour typage de `ref` sur un composant fonctionnel :

```tsx
type InputProps = { placeholder?: string };

const TextInput = React.forwardRef<HTMLInputElement, InputProps>(
  (props, ref) => <input ref={ref} {...props} />
);

// Usage
const ref = useRef<HTMLInputElement>(null);
<TextInput ref={ref} placeholder="Tapez ici" />;
```

---

## 6. Types génériques et utilitaires

### Génériques pour composants réutilisables

```tsx
type ListProps<T> = {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
};

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map((item, i) => <li key={i}>{renderItem(item)}</li>)}</ul>;
}

// Usage
<List
  items={[1, 2, 3]}
  renderItem={(n) => <strong>{n}</strong>}
/>;
```

### Utility Types intégrés

* `Partial<T>` : tous les champs de `T` deviennent optionnels.
* `Required<T>` : tous les champs deviennent obligatoires.
* `Pick<T, K>` / `Omit<T, K>` : sélectionner ou exclure des clés.
* `Record<K, T>` : un objet dont les clés sont `K` et les valeurs `T`.

---

## 7. Bonnes pratiques et pièges à éviter

1. **Ne pas utiliser `any`** : préférez `unknown` si vous devez temporairement assouplir un type, puis affinez-le.
2. **Activer `strictNullChecks`** pour éviter les erreurs de `null` ou `undefined`.
3. **Organiser vos types** dans un dossier `/types` ou `.d.ts` si vous en avez beaucoup.
4. **Séparer logique et présentation** : custom hooks (`useAuth`, `useFetch`) pour la logique, composants « purs » pour l’affichage.
5. **Tester vos types** : `tsc --noEmit` dans votre CI pour valider le typage.

---

## 8. Exemple complet

```tsx
// App.tsx
import React from "react";
import { AuthProvider, useAuth } from "./AuthContext";

const Profile: React.FC = () => {
  const { user } = useAuth();
  return <div>Connecté en tant que : {user?.name ?? "Invité"}</div>;
};

const LoginForm: React.FC = () => {
  const { login } = useAuth();
  const [input, setInput] = React.useState("");
  return (
    <div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Nom d'utilisateur"
      />
      <button onClick={() => login(input)}>Se connecter</button>
    </div>
  );
};

export const App: React.FC = () => (
  <AuthProvider>
    <h1>Mon App React + TypeScript</h1>
    <Profile />
    <LoginForm />
  </AuthProvider>
);
```

---

En résumé, **React + TypeScript** c’est avant tout :

* **Configuration** stricte (tsconfig).
* **Typage** clair des props, state et hooks.
* **Usage avancé** des génériques et utilitaires TS.
* **Bonnes pratiques** pour maintenir un code robuste et évolutif.

Avec ces bases, vous aurez un code plus sûr, plus maintenable et bénéficierez de l’auto‑complétion et des vérifications de TypeScript pour accélérer votre développement.
