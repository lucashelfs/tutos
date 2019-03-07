# Instale o postgres
sudo apt-get install postgresql postgresql-contrib


# Crie um user e DB
sudo -u postgres psql
create database mydb;
create user helfs with encrypted password 'helfs_jota';
grant all privileges on database mydb to helfs;


# Ative seu virtualenv
source /sdd/virtualenvs/monitor/bin/activate && cd /sdd/monitor-trabalhista/ 


# Primeiro passo, fazer um dump do BD em json
python manage.py dumpdata > datadump.json 


# Nas settings do Django, defina as novas configurações de DB
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': os.environ.get('DB_NAME', 'mydb'),
        'USER': os.environ.get('DB_USER', 'helfs'),
        'PASSWORD': os.environ.get('DB_PASS', 'helfs_jota'),
        'HOST': 'localhost',
        'PORT': '5432'
    }
}


# Rode este comando
python manage.py migrate --run-syncdb


# Entre no shell do django
python3 manage.py shell 


# Rode os seguintes comandos 
from django.contrib.contenttypes.models import ContentType
ContentType.objects.all().delete()
quit()


# Fora do shell do Django, carregue o banco de dados antigo para o postgres
python3 manage.py loaddata datadump.json
