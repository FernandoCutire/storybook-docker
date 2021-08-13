# Aprende de contenedores: create-react-app + Storybook + Docker (Con repositorio)

Status: No empezado
¿Publicado?: No

En este artículo crearemos una imagen de docker con base a una aplicación en storybook, esto servirá para que otros desarrolladores puedan correr la aplicación sin proble,as

## 📰 En este artículo aprenderás

1. Cómo empaquetar una aplicación en un contenedor docker
2. El desarrollo de un contenedor para una aplicación JS
3. Cómo ver los puertos del contenedor
4. Solución a errores comunes al empaquetar SPA (Single Page Application)
5. Repositorio con código completo para que puedas [probar la aplicación](https://github.com/FernandoCutire/storybook-docker) 

## Tabla de Contenidos

## ¿Por qué docker?

Respuesta corta, te lo han pedido subirán el storybook a la nube y quieren tener tu sistema de diseño en un pipeline siempre atento.

Puedes leer más sobre docker en mi artículo de Docker para desarrolladores

## Comenzando

Para este ejemplo decidí usar create-react-app para la aplicación `npx create-react-app storybook-docker`. Recuerda que storybook en su documentación dice que su comando `sb init` no funciona sin tener una aplicación antes corriendo, así que es mejor que sigas los pasos.

### Paso 1: Crea la aplicación

`npx create-react-app storybook-docker`

Si ya tienes tu app, usa tu aplicación y ve al siguiente paso

### Paso 2: Storybook

`sb init`

Nota: No funciona en proyectos vacíos por eso usar primero react app

Si ya tienes tu storybook ahora sí vayamos a dockerizar.

### Paso 3: Docker

Para este paso, es recomendable que entiendas como funciona un Dockerfile, te lo explico mejor [aquí](https://github.com/FernandoCutire/storybook-docker)

Este es el código que uso para mi Dockerfile

```docker
# Usar una imagen  
FROM node:current-alpine3.14

# Establecer el directorio de trabajo de nuestro contenedor
WORKDIR /usr/src/app

# Copiar el package.json a la carpeta /app de nuestro contenedor
COPY package.json /app

# Copiará otros archivos de la aplicación
COPY . .

# Ejecutar el comando npm set progress=false && npm install
RUN npm set progress=false && npm install

# Exponer el puerto 8086 de el contenedor docker, fin de documentación
EXPOSE 8086

# Correrá este comando al final cuando se esté corriendo el contenedor
CMD ["npm", "start"]
```

Puedes realizar este y añadirle según tus necesidades, yo solo necesito esos comandos así que lo dejo así.

### Paso 4: docker-compose

Yo para este proyecto uso docker-compose.yml

Puede que no sea necesario tomando en cuenta que solo es una aplicación pero a la hora de manejar más en tu aplicación puede serte útil, así que dejó el código.

```docker
version: "3"
services:
  storybook:
    ports:
      - "8086:8086"
    build: .
```

Aquí se expone el puerto 8086.

Corre tu aplicación con un `docker-compose up`

### Adicional

Algo que me dió problema fue en el package.json, ya que corría mi aplicación dentro del docker pero no podía observarla en el navegador.

Así que revisando mi package.json, expuse el puerto 8086 también ya que por defecto viene otro, te recomiendo que si te da problemas también lo hagas.

```json
{
  "name": "storybook-docker",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.14.1",
    "@testing-library/react": "^11.2.7",
    "@testing-library/user-event": "^12.8.3",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-scripts": "4.0.3",
    "web-vitals": "^1.1.2"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "storybook": "start-storybook -p 8086",
    "build-storybook": "build-storybook -s public"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ],
    "overrides": [
      {
        "files": [
          "**/*.stories.*"
        ],
        "rules": {
          "import/no-anonymous-default-export": "off"
        }
      }
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "devDependencies": {
    "@storybook/addon-actions": "^6.3.7",
    "@storybook/addon-essentials": "^6.3.7",
    "@storybook/addon-links": "^6.3.7",
    "@storybook/node-logger": "^6.3.7",
    "@storybook/preset-create-react-app": "^3.2.0",
    "@storybook/react": "^6.3.7"
  }
}
```

Fijate en el comando que dice `"storybook": "start-storybook -p 8086"`, ese sería el que deberías de cambiar.

## 🔥 Recapitulando

Repasemos lo que aprendiste

- Tener una aplicación corriendo antes de iniciar storybook, una app como la que te genera create-react-app
- Entender como funciona un Dockerfile, para añadir capas según lo necesario
- Verificar los puertos después de montar tu contenedor
- Revisar el package.json con el comando que corras para inicializar storybook por si te da problemas al momento de visualizar tu contenedor en el servidor local.

## 🔚 Fin

Sabes como dockerizar una SPA n un entorno de desarrollo, recuerda el repositorio de github, para que tengas un acceso a todo el código,

[GitHub - FernandoCutire/storybook-docker: This is how you can dockerize a storybook project](https://github.com/FernandoCutire/storybook-docker)
