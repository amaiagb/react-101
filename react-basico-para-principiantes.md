# React Básico para Principiantes

## ¿Qué es React?

React es una biblioteca de JavaScript creada por Facebook para construir interfaces de usuario (UI). En lugar de escribir HTML directamente, React te permite describir cómo debería verse tu interfaz usando JavaScript.

## Configuración Mínima (CDN)

Para empezar con React sin instalación compleja, solo necesitas incluir estos scripts en tu HTML:

```html
<!-- React y ReactDOM (necesarios siempre) -->
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>

<!-- Babel (para poder usar JSX) -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
```

**¿Qué hace cada uno?**
- **React**: La biblioteca principal que permite crear componentes
- **ReactDOM**: Conecta React con el DOM del navegador
- **Babel**: Convierte JSX (que parece HTML) a JavaScript puro que el navegador entiende

## JSX: HTML en JavaScript

JSX es una extensión de JavaScript que te permite escribir código que parece HTML:

```javascript
// Esto es JSX
const elemento = <h1>¡Hola mundo!</h1>;

// Se convierte internamente a esto:
const elemento = React.createElement('h1', null, '¡Hola mundo!');
```

## Componentes: Los Bloques de Construcción

### 🎯 La Regla Fundamental de React

**En React: Los componentes SON funciones, pero no todas las funciones son componentes.**

Un **componente** es una función que:
- ✅ Devuelve JSX (etiquetas HTML)
- ✅ Se puede renderizar en la interfaz
- ✅ Recibe props como parámetros

Una **función normal** es una función que:
- ❌ No devuelve JSX
- ❌ No se renderiza en la interfaz
- ✅ Hace cálculos, lógica o manipula datos

### Componente vs Función Normal

#### ✅ COMPONENTE (devuelve JSX)
```javascript
// Esto es un COMPONENTE
const Header = ({ titulo }) => {
    return <h1>{titulo}</h1>;
};

// Se usa así en JSX:
<Header titulo="Mi App" />
```

#### ❌ FUNCIÓN NORMAL (no devuelve JSX)
```javascript
// Esto es una FUNCIÓN NORMAL
const calcularTotal = (numeros) => {
    return numeros.reduce((total, n) => total + n, 0);
};

// Se usa así:
const total = calcularTotal([1, 2, 3]);
```

### Cómo Identificar si es Componente o Función

#### 📋 Regla Rápida
1. **¿Devuelve etiquetas HTML/JSX?** → Es **componente**
2. **¿Se usa como `<Nombre />`?** → Es **componente**
3. **¿Hace cálculos o lógica?** → Es **función normal**
4. **¿Se llama como `nombre()`?** → Es **función normal**

#### 🎯 Ejemplos Prácticos

```javascript
// ✅ COMPONENTE: Devuelve JSX
const Boton = ({ texto }) => {
    return <button>{texto}</button>;
};

// ❌ FUNCIÓN NORMAL: No devuelve JSX
const formatearFecha = (fecha) => {
    return fecha.toLocaleDateString();
};

// ✅ COMPONENTE: Se renderiza
const App = () => {
    return (
        <div>
            <Boton texto="Clic" />
            <p>Hoy es {formatearFecha(new Date())}</p>
        </div>
    );
};
```

### Componente Básico (01-hola-mundo.html)

```javascript
function HolaMundo() {
    return (
        <div className="container">
            <h1>¡Hola mundo!</h1>
            <p>Este es un componente React básico usando CDN</p>
        </div>
    );
}
```

**Puntos clave:**
- Los componentes siempre empiezan con mayúscula
- Siempre deben devolver un solo elemento (puede tener muchos elementos dentro)
- `className` en JSX es igual a `class` en HTML

## Props: Pasando Datos a Componentes

Las props son como parámetros de función, pero para componentes:

### Componente con Props (02-hola-mundo-propiedades.html)

```javascript
function HolaMundo(props) {
    return (
        <div className="container">
            <h1>Te doy la bienvenida, {props.nombre}</h1>
            <p>{props.mensaje}</p>
        </div>
    );
}

// Al usar el componente, le pasamos las props:
<HolaMundo 
    nombre="Elena" 
    mensaje="Este componente recibe propiedades"
/>
```

**Desestructuración de props (más moderno):**

```javascript
function HolaMundo({ nombre, mensaje }) {
    return (
        <div className="container">
            <h1>Te doy la bienvenida, {nombre}</h1>
            <p>{mensaje}</p>
        </div>
    );
}
```

## Estado: Datos que Cambian

El estado es información que un componente puede modificar y que hace que React vuelva a dibujar el componente:

### Componente con Estado (03-hola-mundo-estado.html)

```javascript
function HolaMundo() {
    // useState crea una variable de estado
    // contador = valor actual
    // setContador = función para actualizar el valor
    const [contador, setContador] = React.useState(0);

    return (
        <div>
            <h1>¡Hola Mundo con React!</h1>
            <div className="counter">
                Contador: {contador}
            </div>
            <button onClick={() => setContador(contador + 1)}>
                Incrementar
            </button>
            <button onClick={() => setContador(0)}>
                Resetear
            </button>
        </div>
    );
}
```

**Reglas del estado:**
- Nunca modifiques el estado directamente: `contador = contador + 1` ❌
- Usa siempre la función setter: `setContador(contador + 1)` ✅
- Cada vez que el estado cambia, React vuelve a renderizar el componente

## Listas y Renderizado Condicional

### Renderizar Listas (00-todolist.html)

```javascript
const ListaTareas = () => {
    const [tareas, setTareas] = React.useState([
        { id: 1, descripcion: "Estudiar Javascript", estado: "Sin empezar" },
        { id: 2, descripcion: "Estudiar PHP", estado: "Sin empezar" },
        { id: 3, descripcion: "Practicar Tailwindcss", estado: "Sin empezar" }
    ]); 
    
    return (
        <div>
            {tareas.map(tarea => (
                <Tarea 
                    key={tarea.id} // ¡Importante! Siempre usa key con listas
                    id={tarea.id} 
                    descripcion={tarea.descripcion} 
                    estado={tarea.estado}
                />
            ))}
        </div>
    );
};
```

**Reglas para listas:**
- Usa `.map()` para transformar arrays en JSX
- Siempre incluye una prop `key` única en cada elemento
- La key ayuda a React a identificar qué elementos cambiaron

## Componentes dentro de Componentes

Puedes componer componentes más grandes usando componentes más pequeños:

```javascript
const App = () => {
    return (
        <div>
            <Header titulo="TODO-List"/>
            <ListaTareas/>
        </div>    
    );
};
```

## Renderizado: Conectar React con el HTML

Finalmente, necesitas decirle a React dónde dibujar tu aplicación:

```html
<div id="root"></div>

<script type="text/babel">
    const root = ReactDOM.createRoot(document.getElementById("root"));
    root.render(<App/>);
</script>
```

**¿Qué hace esto?**
1. `ReactDOM.createRoot()` crea un "contenedor" React en el elemento con id="root"
2. `root.render()` dibuja tu componente principal dentro de ese contenedor

## Resumen de Conceptos Clave

| Concepto | ¿Qué es? | Ejemplo |
|----------|----------|---------|
| **Componente** | Función que devuelve JSX | `function MiComponente() { return <div>Hola</div>; }` |
| **Props** | Datos que pasas a un componente | `<MiComponente nombre="Juan" />` |
| **Estado** | Datos internos que pueden cambiar | `const [contador, setContador] = useState(0);` |
| **JSX** | HTML dentro de JavaScript | `<h1>Hola {nombre}</h1>` |
| **Renderizado** | Proceso de dibujar componentes | `root.render(<App />)` |

## Flujo Básico de una App React

1. **Definir componentes** (funciones que devuelven JSX)
2. **Organizar jerarquía** (componentes dentro de componentes)
3. **Gestionar datos** (props para datos externos, estado para datos internos)
4. **Renderizar** (conectar con el HTML usando ReactDOM)

## Consejos para Principiantes

- **Empieza simple**: Un componente a la vez
- **Piensa en componentes**: Divide tu UI en piezas reutilizables
- **Los datos fluyen hacia abajo**: Las props van de padre a hijo
- **Los eventos fluyen hacia arriba**: Los hijos notifican a los padres mediante funciones
- **Practica con pequeños ejemplos**: Como los que vimos en los archivos HTML

Con estos conceptos básicos, ya puedes empezar a construir aplicaciones simples con React. ¡La clave es practicar y experimentar!
