FROM node:20-alpine

EXPOSE 9000

WORKDIR /var/www/html

COPY src src
COPY public public
COPY package.json \
	package-lock.json \
	vite.config.ts \
	tsconfig.json \
	tsconfig.app.json \
	tsconfig.node.json \
	env.d.ts \
	index.html \
	.eslintrc.cjs \
	./

RUN npm install

CMD ["npm", "run", "dev"]
