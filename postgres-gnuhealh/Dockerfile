FROM postgres:12.10
USER root

#Environment variables
ENV DUMPFILE="gnuhealth-40-demo.sql"

#curl -o /$DUMPFILE -SL "http://health.gnu.org/downloads/postgres_dumps/$DUMPFILE"

ADD $DUMPFILE /docker-entrypoint-initdb.d/

#RUN echo "host all all all trust" >> pg_hba.conf

#RUN su - postgres -c "createuser --createdb --no-createrole --no-superuser gnuhealth"


#FROM postgres:12.10

# Use our local cache
#RUN echo 'Acquire::http { Proxy "http://apt-cacher:9999"; };' >> /etc/apt/apt.conf.d/01proxy

#ENV DUMPFILE="gnuhealth-40-demo.sql.gz"
#ENV SQLFILE="health301.sql"

#VOLUME /var/lib/postgresql
#RUN apt-get update && apt-get install -y --no-install-recommends \
#  gzip curl ca-certificates tar \
#  && rm -rf /var/lib/apt/lists/* \
#  && curl -o /$DUMPFILE -SL "http://health.gnu.org/downloads/postgres_dumps/$DUMPFILE" \  
#  && mv $DUMPFILE demo.sql.gz \
#  && apt-get purge -y --auto-remove curl ca-certificates

#COPY gnuhealth.sh /docker-entrypoint-initdb.d/
