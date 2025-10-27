# Dockerfile client
FROM node:lts-alpine

# Définir le répertoire de travail
WORKDIR /app

# Copier package.json et package-lock.json
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier le reste des fichiers
COPY . .

# Construire l'application React
RUN npm run build

# Installer un serveur HTTP léger
RUN npm install -g serve

# Exposer le port 3000
EXPOSE 3000

# Démarrer le serveur pour servir les fichiers build
CMD ["serve", "-s", "build", "-l", "3000"]
