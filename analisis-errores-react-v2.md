# Análisis de Errores - Aplicación de Colección de Videojuegos (Versión 2)

## Descripción General de la Aplicación

Esta aplicación es una colección de videojuegos arcade que permite:

- Ver una lista de 6 juegos predefinidos
- Agregar nuevos juegos mediante un formulario
- Marcar juegos como "jugados" (aunque con errores graves)
- Mostrar estadísticas totales (aunque están hardcodeadas)

## Flujo del Código

### 1. Estructura de Componentes

```
App (Componente Principal)
├── Header (muestra título y estadísticas - ERRORES)
├── FormularioJuego (para agregar nuevos juegos - ERRORES)
└── JuegoCard (tarjeta individual para cada juego - ERRORES)
```

### 2. Flujo de Datos Intended

1. **App** mantiene el estado principal:
   - `juegos`: Array con todos los juegos
   - `totalJugados`: Contador global de juegos jugados

2. **Datos deberían fluir hacia abajo**:
   - App → Header (título, estadísticas)
   - App → FormularioJuego (función para agregar)
   - App → JuegoCard (datos de cada juego)

3. **Eventos deberían fluir hacia arriba**:
   - FormularioJuego → App (agregar nuevo juego)
   - JuegoCard → App (marcar como jugado) ← **ROTO**

## Errores Identificados y Explicación Detallada

---

### ❌ ERROR 1: Header con Datos Hardcodeados (Líneas 20-38)

**Problema:** El componente Header muestra números fijos en lugar de datos dinámicos.

**Código actual:**

```javascript
const Header = ({ titulo }) => {
  return (
    <div className="bg-white p-5 rounded-lg mb-5 text-center">
      <h1 className="text-purple-800 mb-2 text-3xl font-bold">{titulo}</h1>
      <p className="text-gray-700">Gestiona tu colección de videojuegos</p>

      <div className="bg-purple-100 p-4 rounded mt-4 flex justify-around">
        <div className="text-center">
          <strong className="block text-2xl text-purple-500">6</strong>{" "}
          {/* ❌ HARDCODEADO */}
          <span className="text-gray-700">Total Juegos</span>
        </div>
        <div className="text-center">
          <strong className="block text-2xl text-purple-500">0</strong>{" "}
          {/* ❌ HARDCODEADO */}
          <span className="text-gray-700">Jugados</span>
        </div>
      </div>
    </div>
  );
};
```

**¿Por qué es un error?**

- Los números "6" y "0" están escritos directamente en el código
- No se actualizan cuando se agregan nuevos juegos
- No reflejan el estado real de la aplicación
- El componente recibe solo `titulo` pero debería recibir también las estadísticas

**Solución correcta:**

```javascript
const Header = ({ titulo, totalJuegos, totalJugados }) => {
  return (
    <div className="bg-white p-5 rounded-lg mb-5 text-center">
      <h1 className="text-purple-800 mb-2 text-3xl font-bold">{titulo}</h1>
      <p className="text-gray-700">Gestiona tu colección de videojuegos</p>

      <div className="bg-purple-100 p-4 rounded mt-4 flex justify-around">
        <div className="text-center">
          <strong className="block text-2xl text-purple-500">
            {totalJuegos}
          </strong>
          <span className="text-gray-700">Total Juegos</span>
        </div>
        <div className="text-center">
          <strong className="block text-2xl text-purple-500">
            {totalJugados}
          </strong>
          <span className="text-gray-700">Jugados</span>
        </div>
      </div>
    </div>
  );
};
```

---

### ❌ ERROR 2: Formulario sin Estado Controlado (Líneas 41-94)

**Problema:** El formulario no usa estado de React para controlar los inputs.

**Código actual:**

```javascript
const FormularioJuego = ({ onAgregar }) => {
  const titulo = ""; // ❌ ERROR: Variable normal, no estado
  const desarrollo = ""; // ❌ ERROR: Variable normal, no estado
  const año = ""; // ❌ ERROR: Variable normal, no estado

  const handleSubmit = (e) => {
    e.preventDefault();

    if (titulo && desarrollo && año) {
      // ❌ Siempre será falso
      onAgregar({
        titulo,
        desarrollo,
        año: parseInt(año),
      });
    }
  };

  return (
    <div className="bg-white p-5 rounded-lg mb-5">
      <h2 className="text-purple-800 mb-4 text-2xl font-bold">
        Agregar nuevo juego
      </h2>
      <form onSubmit={handleSubmit}>
        <div className="flex flex-wrap gap-4 mb-4">
          <div className="flex-1 min-w-[200px] flex flex-col">
            <label className="mb-1 text-purple-600 font-bold">Título:</label>
            <input
              type="text"
              placeholder="Ej: Tetris"
              // ❌ FALTAN: value y onChange
              className="p-2 border border-gray-300 rounded text-sm focus:outline-none focus:border-purple-500"
            />
          </div>
          {/* ... otros inputs con el mismo problema */}
        </div>
        <button
          type="submit"
          className="w-full p-2.5 bg-purple-600 text-white border-none rounded text-base hover:bg-purple-800 cursor-pointer"
        >
          Agregar juego a la colección
        </button>
      </form>
    </div>
  );
};
```

**¿Por qué son errores?**

1. **Variables normales vs estado**: `const titulo = ''` crea una variable normal que siempre será una cadena vacía. No es estado de React.

2. **Inputs no controlados**: Los inputs no tienen `value` ni `onChange`, por lo que React no sabe qué contienen.

3. **Validación siempre falsa**: Como `titulo`, `desarrollo` y `año` siempre son cadenas vacías, la condición `if (titulo && desarrollo && año)` nunca será verdadera.

4. **No se puede leer el valor**: No hay forma de obtener lo que el usuario escribe en los inputs.

**Solución correcta:**

```javascript
const FormularioJuego = ({ onAgregar }) => {
  // ✅ CORRECTO: Usar useState para cada campo
  const [titulo, setTitulo] = React.useState("");
  const [desarrollo, setDesarrollo] = React.useState("");
  const [año, setAño] = React.useState("");

  const handleSubmit = (e) => {
    e.preventDefault();

    if (titulo && desarrollo && año) {
      // ✅ Ahora funciona
      onAgregar({
        titulo,
        desarrollo,
        año: parseInt(año),
      });
      // ✅ Limpiar el formulario después de enviar
      setTitulo("");
      setDesarrollo("");
      setAño("");
    }
  };

  return (
    <div className="bg-white p-5 rounded-lg mb-5">
      <h2 className="text-purple-800 mb-4 text-2xl font-bold">
        Agregar nuevo juego
      </h2>
      <form onSubmit={handleSubmit}>
        <div className="flex flex-wrap gap-4 mb-4">
          <div className="flex-1 min-w-[200px] flex flex-col">
            <label className="mb-1 text-purple-600 font-bold">Título:</label>
            <input
              type="text"
              value={titulo} // ✅ Conectar con el estado
              onChange={(e) => setTitulo(e.target.value)} // ✅ Actualizar estado
              placeholder="Ej: Tetris"
              className="p-2 border border-gray-300 rounded text-sm focus:outline-none focus:border-purple-500"
            />
          </div>
          {/* ... otros inputs con value y onChange */}
        </div>
        <button
          type="submit"
          className="w-full p-2.5 bg-purple-600 text-white border-none rounded text-base hover:bg-purple-800 cursor-pointer"
        >
          Agregar juego a la colección
        </button>
      </form>
    </div>
  );
};
```

---

### ❌ ERROR 3: Modificación de Variable Normal en JuegoCard (Líneas 97-121)

**Problema:** Se intenta modificar una variable normal en lugar de usar estado.

**Código actual:**

```javascript
const JuegoCard = ({ titulo, desarrollo, año }) => {
  let vecesJugado = 0; // ❌ ERROR: Variable normal, no estado

  const marcarComoJugado = () => {
    vecesJugado = vecesJugado + 1; // ❌ ERROR: Modificando variable normal
    console.log("Jugado:", vecesJugado); // Solo se ve en consola
  };

  return (
    <div className="bg-white rounded-lg p-5 flex-1 min-w-[280px]">
      <h3 className="text-purple-800 mb-2 text-xl font-bold">{titulo}</h3>
      <p className="text-purple-700 italic mb-2.5">por {desarrollo}</p>
      <p className="text-purple-500 text-sm mb-4">Año {año}</p>

      <div className="flex gap-2.5">
        <button
          className="flex-1 p-2 border-none rounded cursor-pointer text-sm bg-purple-300 text-white hover:bg-purple-400"
          onClick={marcarComoJugado}
        >
          Jugado <span className="font-bold ml-1">({vecesJugado})</span>{" "}
          {/* ❌ Siempre mostrará 0 */}
        </button>
      </div>
    </div>
  );
};
```

**¿Por qué son errores?**

1. **Variable normal vs estado**: `let vecesJugado = 0` crea una variable local que se reinicia cada vez que el componente se renderiza.

2. **No hay re-renderizado**: Aunque modificas la variable, React no sabe que debe volver a dibujar el componente.

3. **Valor siempre 0**: Cada vez que el componente se renderiza, `vecesJugado` vuelve a 0.

4. **No se comunica con el padre**: El componente App nunca sabe que se marcó un juego como jugado.

**Solución correcta:**

```javascript
const JuegoCard = ({
  titulo,
  desarrollo,
  año,
  vecesJugado,
  onMarcarJugado,
}) => {
  return (
    <div className="bg-white rounded-lg p-5 flex-1 min-w-[280px]">
      <h3 className="text-purple-800 mb-2 text-xl font-bold">{titulo}</h3>
      <p className="text-purple-700 italic mb-2.5">por {desarrollo}</p>
      <p className="text-purple-500 text-sm mb-4">Año {año}</p>

      <div className="flex gap-2.5">
        <button
          className="flex-1 p-2 border-none rounded cursor-pointer text-sm bg-purple-300 text-white hover:bg-purple-400"
          onClick={onMarcarJugado} // ✅ Llamar a la función del padre
        >
          Jugado <span className="font-bold ml-1">({vecesJugado})</span>{" "}
          {/* ✅ Valor del estado del padre */}
        </button>
      </div>
    </div>
  );
};
```

---

### ❌ ERROR 4: Desconexión entre App y JuegoCard (Líneas 187-194)

**Problema:** No hay comunicación entre el componente App y las tarjetas de juego.

**Código actual:**

```javascript
<div className="flex flex-wrap gap-5">
  {juegos.map((juego) => (
    <JuegoCard
      key={juego.id}
      titulo={juego.titulo}
      desarrollo={juego.desarrollo}
      año={juego.año}
      // ❌ FALTAN: vecesJugado y onMarcarJugado
    />
  ))}
</div>
```

**Problemas:**

1. No se pasa `vecesJugado` (los juegos no tienen esta propiedad en el estado)
2. No se pasa `onMarcarJugado` (no hay función para manejar el clic)
3. Los juegos en el estado no tienen contador de veces jugado

---

### ❌ ERROR 5: Estado Inconsistente (Líneas 125-164)

**Problema:** Los juegos en el estado no tienen la propiedad `vecesJugado`.

**Código actual:**

```javascript
const [juegos, setJuegos] = useState([
  {
    id: 1,
    titulo: "Pac-Man",
    desarrollo: "Namco",
    año: 1980,
    // ❌ FALTA: vecesJugado: 0
  },
  // ... otros juegos con el mismo problema
]);
```

**Problemas:**

- No hay forma de trackear cuántas veces se jugó cada juego
- `totalJugados` existe pero no hay forma de actualizarlo
- No hay lógica para incrementar contadores individuales

---

### ❌ ERROR 6: Función `incrementarJugados` sin Uso (Líneas 174-176)

**Problema:** Hay una función definida pero nunca se llama.

**Código actual:**

```javascript
const incrementarJugados = () => {
  setTotalJugados(totalJugados + 1);
};
```

**Problema:** Esta función existe pero nunca se llama desde ningún lugar, por lo que `totalJugados` siempre será 0.

## Flujo Correcto de la Aplicación

### 1. Estado Centralizado en App

```javascript
const App = () => {
  const [juegos, setJuegos] = useState([
    {
      id: 1,
      titulo: "Pac-Man",
      desarrollo: "Namco",
      año: 1980,
      vecesJugado: 0, // ✅ Agregar contador individual
    },
    // ... otros juegos con vecesJugado: 0
  ]);

  // ✅ Función para marcar un juego como jugado
  const marcarComoJugado = (juegoId) => {
    setJuegos(juegos.map((juego) => {
        if (juego.id === juegoId) {
          if (juego.vecesJugado === 0) {
            incrementarJugados();
          }
          return {
            ...juego,
            vecesJugado: juego.vecesJugado + 1,
          };
        }
        return juego;
      }),
    );
  };

  // ✅ Calcular total jugados dinámicamente
  const totalJugados = juegos.reduce(
    (total, juego) => total + juego.vecesJugado,
    0,
  );

  return (
    <div className="max-w-6xl mx-auto">
      <Header
        titulo="Mi Colección de Videojuegos Arcade"
        totalJuegos={juegos.length}
        totalJugados={totalJugados} // ✅ Pasar valor calculado
      />

      <FormularioJuego onAgregar={agregarJuego} />

      <div className="flex flex-wrap gap-5">
        {juegos.map((juego) => (
          <JuegoCard
            key={juego.id}
            titulo={juego.titulo}
            desarrollo={juego.desarrollo}
            año={juego.año}
            vecesJugado={juego.vecesJugado} // ✅ Pasar contador
            onMarcarJugado={() => marcarComoJugado(juego.id)} // ✅ Pasar función
          />
        ))}
      </div>
    </div>
  );
};
```

## Resumen de Errores y Soluciones

| Error                              | Problema                                  | Solución                                            |
| ---------------------------------- | ----------------------------------------- | --------------------------------------------------- |
| **Header hardcodeado**             | Números fijos en lugar de datos dinámicos | Pasar `totalJuegos` y `totalJugados` como props     |
| **Formulario sin estado**          | Variables normales en lugar de `useState` | Usar `useState` para cada campo del formulario      |
| **Inputs no controlados**          | Faltan `value` y `onChange`               | Conectar inputs con estado de React                 |
| **Variable normal en JuegoCard**   | `let vecesJugado = 0` no persiste         | Pasar `vecesJugado` como prop del estado del padre  |
| **Sin comunicación App-JuegoCard** | No hay función para manejar clics         | Crear `marcarComoJugado` en App y pasarla como prop |
| **Estado incompleto**              | Juegos sin `vecesJugado`                  | Agregar `vecesJugado: 0` a cada juego               |
| **Función sin uso**                | `incrementarJugados` nunca se llama       | Eliminar o integrar en la lógica principal          |

## Conceptos React que se Violan

1. **Estado unidireccional**: Los datos deberían fluir hacia abajo, los eventos hacia arriba
2. **Componentes controlados**: Los inputs deben estar conectados al estado
3. **Elevación de estado**: El estado compartido debe estar en el componente padre común
4. **Inmutabilidad**: Nunca modificar el estado directamente
5. **Props de solo lectura**: Las props son inmutables

## Cómo Arreglar la Aplicación (Pasos)

1. **Agregar `vecesJugado: 0`** a cada juego en el estado inicial
2. **Crear `marcarComoJugado(juegoId)`** en App que actualice el array de juegos
3. **Pasar `totalJuegos` y `totalJugados`** al Header como props
4. **Convertir el formulario a controlado** usando `useState` para cada campo
5. **Agregar `value` y `onChange`** a todos los inputs del formulario
6. **Pasar `vecesJugado` y `onMarcarJugado`** a cada JuegoCard
7. **Calcular `totalJugados` dinámicamente** o eliminar si no se necesita

Con estos cambios, la aplicación funcionaría correctamente siguiendo todos los principios fundamentales de React.
