# Dockerfile serveur
FROM node:lts-alpine

# Définir le répertoire de travail
WORKDIR /app

# Copier package.json et package-lock.json
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier le reste des fichiers du serveur
COPY . .

# Définir la variable d'environnement MONGO_URI
ENV MONGO_URI=mongodb://mongodb:27017/mern-db

# Exposer le port 9000
EXPOSE 9000

# Démarrer l'application
CMD ["npm", "start"]
