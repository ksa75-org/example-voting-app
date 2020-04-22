FROM maven:3-alpine

WORKDIR /app

COPY . .

RUN mvn -s settings.xml package -Dskiptests && \
    mv target/worker-jar-with-dependencies.jar /run/worker.jar && \
    rm -rf /app/*

CMD java -jar /run/worker.jar