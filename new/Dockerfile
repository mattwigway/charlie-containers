# Dockerfile for the code originally used in the Charlie paper
FROM gradle:8-jdk11

RUN git clone https://github.com/mattwigway/r5.git
RUN cd r5 && git switch boston-fare-corrections-merge && gradle shadowJar --stacktrace

RUN mkdir graph
COPY boston.pbf graph/boston.pbf
COPY baseline.zip graph/baseline.zip

# build graph
RUN ls r5/build/libs

# build graph
RUN java -Xmx4G -cp r5/build/libs/r5-*.jar com.conveyal.r5.point_to_point.PointToPointRouterServer --build graph

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

CMD [ "bash", "-c", "java -Xmx4G -cp r5/build/libs/r5-*.jar com.conveyal.r5.point_to_point.PointToPointRouterServer --graphs graph & (cd fareto && . $NVM_DIR/nvm.sh && yarn start)" ]