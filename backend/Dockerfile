FROM mhart / alpine-node: 8.11.4
WORKDIR / api
COPY package * .json / api /
RUN npm install
COPY. / api /
EXPOSE 8080
CMD ["npm", "start"]