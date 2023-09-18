# Dockerfile for the code originally used in the Charlie paper
FROM eclipse-temurin:8

# https://stackoverflow.com/questions/25899912/how-to-install-nvm-in-docker

RUN apt-get update
RUN apt-get install -y git maven
RUN git clone https://github.com/mattwigway/r5.git
RUN cd r5 && git switch matt/original-charlie-code && mvn package -DskipTests

RUN mkdir graph
COPY boston.pbf graph/boston.pbf
COPY baseline.zip graph/baseline.zip

# build graph
RUN java -Xmx4G -cp r5/target/v4.1.0-*.jar com.conveyal.r5.point_to_point.PointToPointRouterServer --build graph

# get the frontend
RUN git clone https://github.com/conveyal/fareto.git

RUN wget https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh
RUN bash install.sh
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && \
    nvm install 18 && \
    nvm use 18 && \
    npm install --global yarn && \
    cd fareto && \
    yarn install && \
    yarn add react-scripts@5

EXPOSE 3000

CMD [ "bash", "-c", "java -Xmx4G -cp r5/target/v4.1.0-*.jar com.conveyal.r5.point_to_point.PointToPointRouterServer --graphs graph & (cd fareto && . $NVM_DIR/nvm.sh && yarn start)" ]