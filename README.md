# 1. STOP ALL SERVICES (Prevent further errors)
sudo systemctl stop gunicorn
sudo systemctl stop nginx

# 2. EMERGENCY BACKUP
cp db.sqlite3 emergency_backup_$(date +%Y%m%d_%H%M%S).db

# 3. FIX DJANGO INSTALLATION
source venv/bin/activate
pip uninstall django -y
pip cache purge
pip install Django==4.2.7

# 4. TEST DJANGO FIX
python -c "import django; print('✅ Django version:', django.get_version())"

# 5. TEST DJANGO MANAGEMENT
python manage.py check
/////////////////////////////////
# 1. COMPLETE VIRTUAL ENVIRONMENT REBUILD
deactivate
rm -rf venv/
python3 -m venv venv
source venv/bin/activate

# 2. UPGRADE PIP
pip install --upgrade pip

# 3. INSTALL REQUIREMENTS FRESH
pip install -r requirements.txt

# 4. VERIFY DJANGO
python -c "
import django
from django.db import migrations
from django.core.management import execute_from_command_line
print('✅ Django fully functional')
"
////////////////////////
# Test 1: Django import
python -c "import django; print(django.get_version())"

# Test 2: Management commands
python manage.py check --deploy

# Test 3: Database connection  
python manage.py shell << 'EOF'
from django.db import connection
cursor = connection.cursor()
print("✅ Database working")
EOF

# Test 4: Migration system
python manage.py showmigrations
////////////////////////////////////
# Only after Django is working:
python manage.py makemigrations users core menu rooms inventory notifications staff tables bills
python manage.py migrate --fake-initial
python manage.py migrate

# Restart services
sudo systemctl start gunicorn  
sudo systemctl start nginx
/////////////////
source venv/bin/activate && pip uninstall django -y && pip install Django==4.2.7 && python -c "import django; print('Django OK')" && python manage.py check
////////////////////////////////
