FROM python:3.10-slim-buster

# inside the container
# make a new folder inside container
WORKDIR /backend-flask

# Outside the container -> Inside the container
# this contains the libraries we want to install  to run tha app
COPY requirements.txt requirements.txt

# inside the container
# install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside container  -> Inside container
# . means everything in the current directory
# . (first period) everything in /backend-flask(outside container)
# . (second period) everything in / backend-flask(inside container)
COPY . .

# set enviroment variables
# inside the container and will remain set when the container is running.
ENV FLASK_ENV=development

EXPOSE ${PORT}

# CMD(command)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]