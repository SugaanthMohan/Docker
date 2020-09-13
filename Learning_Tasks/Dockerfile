# USE THE UBUNTU IMAGE
FROM ubuntu:18.04


# SET THE MAINTAINER FOR EMAIL UPDATES
MAINTAINER sugaanth.mohan@gmail.com

# UPDATE INSTALL HANDLE
RUN apt-get update

# ADD THE GNUPG2 FOR UBUNTU OPERATION
RUN apt-get install -y gnupg2


# ==========================================================================================================

WORKDIR /tmp/postgres10

###################################
# DOCKER POSTGRES STARTS HERE     #
###################################

RUN apt-get install -y wget ca-certificates

RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" >> /etc/apt/sources.list.d/pgdg.list

RUN apt-get update

RUN apt-get update && apt-get install -y software-properties-common postgresql postgresql-contrib
 
# Note: The official Debian and Ubuntu images automatically ``apt-get clean``
# after each ``apt-get``

# Run the rest of the commands as the ``postgres`` user created by the ``postgres-9.3`` package when it was ``apt-get installed``
USER postgres

# Create a PostgreSQL role named ``docker`` with ``docker`` as the password and
# then create a database `docker` owned by the ``docker`` role.
# Note: here we use ``&&\`` to run commands one after the other - the ``\``
#       allows the RUN command to span multiple lines.
RUN    /etc/init.d/postgresql start &&\
    psql -U postgres -c "CREATE USER learningtasker WITH SUPERUSER PASSWORD 'm3rg3r';" &&\
    psql -U postgres -c "ALTER USER postgres WITH PASSWORD 'm3rg3r';" &&\
    createdb -O learningtasker learningtasks

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/10/main/pg_hba.conf

# And add ``listen_addresses`` to ``/etc/postgresql/9.3/main/postgresql.conf``
RUN echo "listen_addresses='*'" >> /etc/postgresql/10/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432



###################################
# DOCKER POSTGRES SETUP ENDS HERE #
###################################

# ==========================================================================================================

###################################
# DOCKER PYTHON SETUP STARTS HERE #
###################################

USER root

# ADD OTHER ADDITIONAL REQUIREMENTS HERE
RUN apt-get install -y git less vim grep sed

# ADD REQUIRED PACKAGES FOR INSTALL 
RUN apt-get install -y build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libsqlite3-dev libreadline-dev libffi-dev wget libbz2-dev

# MOVE WORKING DIRECTORY TO TEMP 
WORKDIR /tmp/python_install

# DOWNLOAD THE TAR FILE 
RUN wget https://www.python.org/ftp/python/3.8.0/Python-3.8.0.tgz

# EXTRACT THE GZIPPED TAR BALL
RUN tar -xf Python-3.8.0.tgz

# MOVE INSIDE THE DIRECTORY
WORKDIR ./Python-3.8.0/

RUN chmod +x configure

RUN ./configure --enable-optimizations

# MAKE INSTALL
RUN make altinstall

# python3.8 --version
RUN python3.8 --version

# PULL THE GIT PROJECT INTO HERE
WORKDIR /tmp/python_requirements/

COPY requirements.txt ./

RUN python3.8 -m pip install --upgrade pip

RUN apt-get install -y postgresql
RUN apt-get install -y python-psycopg2
RUN apt-get install -y libpq-dev

RUN python3.8 -m pip install psycopg2
RUN python3.8 -m pip install pandas
RUN python3.8 -m pip install numpy
RUN python3.8 -m pip install cryptography

###################################
# DOCKER PYTHON SETUP STOPS HERE #
###################################


#############################
# PROJECT SETUP STARTS HERE #
#############################


# SETUP YOUR USER WORK DIRECTORY HERE
WORKDIR /home/sugaanth/

RUN git clone https://SugaanthMohan:<TOKEN>@github.com/SugaanthMohan/<REPOSITORY>.git --branch master --single-branch ./<REPOSITORY>/

RUN chmod -R 777 /home/sugaanth/

# USER POSTGRES
USER postgres

# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/10/bin/postgres", "-D", "/var/lib/postgresql/10/main", "-c", "config_file=/etc/postgresql/10/main/postgresql.conf"]

WORKDIR /home/sugaanth/


#############################
# PROJECT SETUP ENDS HERE   #
#############################
