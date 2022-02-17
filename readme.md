# Pages

GitHub: https://github.com/InmobilApp

Deploy: https://inmobil-app.herokuapp.com/

# Indice

<ul>
  <li><a href="#property-schema-refs">Property Schema</a></li>
</ul>

# Schemas

<h3 id="property-schema-refs"><a href="#property-schema-refs">Property Schema</a></h3>

```javascript
const propertySchema = new moongose.Schema({
  typeProperty: {
    type: String,
    enum: {
      values: ["casa", "apartamento", "local", "finca"],
      message: "{VALUE} is not supported",
    },
  },
  date: {
    type: Date,
    default: Date.now,
  },
  ubication: {
    city: { type: String, required: true },
    neighbourhooh: { type: String, required: true },
    adress: { type: String, required: true },
  },
  images: [String],
  state: {
    type: String,
    enum: {
      values: ["available", "unavailable", "reserved"],
      message: "{VALUE} is not supported",
    },
    default: "available",
  },
  rentalPrice: String,
  reviews: [
    {
      user: String,
      content: String,
      score: {
        type: Number,
        min: 0,
        max: 5,
      },
      date: {
        type: Date,
        default: Date.now,
      },
    },
  ],
  description: String,
  details: {
    area: String,
    rooms: String,
    baths: String,
    garage: Boolean,
  },
  agentID: {
    type: Schema.Types.ObjectId,
    ref: "Agent",
  },
});
```

# Routes

Cuando pasa una ruta desconocida para el "Servidor"

```javascript
response.status(404).send({ error: "unknown endpoint" });
```

Cuando pasas un "id" no valido va a retornar

```javascript
response.status(400).send({ error: "malformatted id" });
```

Cuando ocurre un error al hacer un PUT o POST con el Schema va a retornar un message relacionado con el error.

```javascript
response.status(400).json({ error: error.message });
```

## Rutas "/api/properties"

### GET "/api/properties"

Retorna un arreglo con todas las propiedades guardadas en la base de datos.

### POST "/api/properties"

Por medio de "body" recibe un objeto con las propiedades requeridas para crear una propiedad, algunas propiedades se inicializan por defecto como la fecha, y el estado de la propiedad, por defecto es "avalidable".

```javascript
{
  typeProperty: "casa",
  ubication: {
    city: "Bogota",
    neighbourhooh: "usme",
    adress: "Cll 22B etc...",
  },
  images: ["url", "url1"],
  rentalPrice: "500",
  description: "Decripcion del inmueble",
  details: {
    area: "30",
    rooms: "2",
    baths: "2",
    garage: true,
  }
}
...

...
"Objeto retornado al hacer este post"
{
    "typeProperty": "casa",
    "ubication": {
        "city": "Bogota",
        "neighbourhooh": "usme",
        "adress": "Cll 22B etc..."
    },
    "images": [
        "url",
        "url1"
    ],
    "state": "available",
    "rentalPrice": "500",
    "description": "Decripcion del inmueble",
    "details": {
        "area": "30",
        "rooms": "2",
        "baths": "2",
        "garage": true
    },
    "date": "2022-02-16T02:40:10.671Z",
    "reviews": [],
    "id": "620c640ab2c22c087c7d326d"
}
```

### GET "/api/properties/620c640ab2c22c087c7d326d"

Al hacer un get con "id" a "/api/properties/:id" retorna la propiedad que coincida con ese ID.

```json
{
  "ubication": {
    "city": "Bogota",
    "neighbourhooh": "usme",
    "adress": "Cll 22B etc..."
  },
  "details": {
    "area": "30",
    "rooms": "2",
    "baths": "2",
    "garage": true
  },
  "typeProperty": "casa",
  "images": ["url", "url1"],
  "state": "available",
  "rentalPrice": "500",
  "description": "Decripcion del inmueble",
  "date": "2022-02-16T02:40:10.671Z",
  "reviews": [],
  "id": "620c640ab2c22c087c7d326d"
}
```

### DELETE "/api/properties/620c640ab2c22c087c7d326d"

Borra la propiedad con el id pasado por parametro de la base de datos.
_______________________________________________________________________________________________________________________________________________________________________________

<h3 id="agent-schema-refs"><a href="#agent-schema-refs">Agent Schema</a></h3>

```javascript
const agentSchema = new Schema({
  name: {
    type: String,
    required: true,
  },
  dni: {
    type: String,
    required: true,
    unique: true,
  },
  adress: {
    type: String,
    required: true,
  },
  phone: String,
  age: String,
  properties: [
    {
      type: Schema.Types.ObjectID,
      ref: "Property",
    },
  ],
  permissions: {
    crudProperty: Boolean,
  },
  admindID: {
    type: Schema.Types.ObjectID,
    ref: "Admin",
  },
});
```

# Errores

Cuando pasa una ruta desconocida para el "Servidor"

```javascript
response.status(404).send({ error: "unknown endpoint" });
```

Cuando pasas un "id" no valido va a retornar

```javascript
response.status(400).send({ error: "malformatted id" });
```

Cuando ocurre un error al hacer un PUT o POST con el Schema va a retornar un message relacionado con el error.

response.status(400).json({ error: error.message });

# Rutas "api/agents"

### GET "api/agents"

Retorna un arreglo con todas los agentes guardados en la base de datos y su relacion con una propiedad

```json
{
        "permissions": {
            "crudProperty": true
        },
        "name": "David",
        "dni": "1012437698",
        "adress": "Direccion random en bucaramanga",
        "phone": "98565621",
        "age": "22",
        "properties": [
            "620e82d3a72c0b07f4a80ae3" // --> Relacion
        ],
        "id": "620e808a8034ceb08eb309a8"
    }
```
### GET "api/agents/:id"

Retorna un arreglo con todas las propiedades guardadas en la base de datos y su relacion con una propiedad (Muestra la info completa de esa propiedad)

```json
{
    "permissions": {
        "crudProperty": true
    },
    "name": "David",
    "dni": "1012437698",
    "adress": "Direccion random en bucaramanga",
    "phone": "98565621",
    "age": "22",
    "properties": [ // -----> Info completa de esa propiedad
        {
            "ubication": {
                "city": "Bogota",
                "neighbourhooh": "usme",
                "adress": "Cll 22B etc..."
            },
            "details": {
                "area": "30",
                "rooms": "2",
                "baths": "2",
                "garage": true
            },
            "typeProperty": "casa",
            "images": [
                "url",
                "url1"
            ],
            "state": "available",
            "rentalPrice": "500",
            "description": "Decripcion del inmueble",
            "agentID": "620e808a8034ceb08eb309a8",
            "date": "2022-02-17T17:16:03.063Z",
            "reviews": [],
            "id": "620e82d3a72c0b07f4a80ae3"
        }
    ],
    "id": "620e808a8034ceb08eb309a8"
}
```

### POST "api/agents"

Por medio de "body" recibe un objeto con las propiedades requeridas para crear una propiedad

```javascript
{
  adminID: "620e806162c266edbeaca6f7",
  dni: "1075982698",
  name: "Gabriel",
  adress: "Springfield",
  phone: "7591546",
  age: "30",
  permissions: {
    crudProperty: true
  }
}
...
...
"JSON retornado al hacer este post"
{
    "name": "Gabriel",
    "dni": "1075982698",
    "adress": "Springfield",
    "phone": "7591546",
    "age": "30",
    "properties": [],
    "permissions": {
        "crudProperty": true
    },
    "id": "620ed23cd2696158e8b89619"
}
```

### DELETE "api/agents/620e808a8034ceb08eb309a8"

Borra la propiedad con el id pasado por parametro de la base de datos.
