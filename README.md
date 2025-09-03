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
////////////////////////////////
# 1. STOP SERVICES
sudo systemctl stop gunicorn

# 2. ACTIVATE ENVIRONMENT  
source venv/bin/activate

# 3. FIX CORRUPTED PACKAGES
pip uninstall certifi pytz tzdata -y

# 4. INSTALL CORRECT VERSIONS
pip install certifi==2023.7.22
pip install pytz==2023.3  
pip install tzdata==2023.3

# 5. ADD MISSING PACKAGE
pip install xhtml2pdf==0.2.13

# 6. TEST DJANGO
python -c "
import django
from django.db import migrations  
print('✅ Django fixed!')
"

# 7. TEST MANAGEMENT COMMANDS
python manage.py check

/////////////////////////////////
# Core Django
Django==4.2.7
djangorestframework==3.14.0
djangorestframework-simplejwt==5.3.0

# Database & CORS
psycopg2-binary==2.9.9
django-cors-headers==4.3.1

# Environment & Server
python-decouple==3.8
python-dotenv==1.0.0
gunicorn==23.0.0
whitenoise==6.6.0

# File Processing & PDFs
Pillow==10.0.1
reportlab==4.0.7
xhtml2pdf==0.2.13

# API Documentation
drf-yasg==1.21.10

# Core Utilities
python-dateutil==2.9.0.post0
pytz==2023.3
tzdata==2023.3
certifi==2023.7.22
/////////////////////////////////////
#!/bin/bash

echo "ߔ Fixing corrupted Django environment..."

# Backup current requirements
cp requirements.txt requirements_backup.txt

# Stop services
sudo systemctl stop gunicorn

# Fix environment
source venv/bin/activate

# Remove corrupted packages
echo "ߗ️ Removing corrupted packages..."
pip uninstall certifi pytz tzdata -y

# Install correct versions
echo "ߓ Installing fixed packages..."
pip install certifi==2023.7.22 pytz==2023.3 tzdata==2023.3

# Add missing PDF package
pip install xhtml2pdf==0.2.13

# Reinstall Django to be safe
echo "ߔ Reinstalling Django..."
pip uninstall django -y
pip install Django==4.2.7

# Test Django
echo "ߧ Testing Django..."
python -c "
import django
from django.db import migrations
print('✅ Django working!')
"

# If successful, test management
if [ $? -eq 0 ]; then
    echo "✅ Django fixed! Testing management commands..."
    python manage.py check
    
    if [ $? -eq 0 ]; then
        echo "ߎ Complete fix successful!"
        echo "ߔ You can now run migrations"
        
        # Restart services
        sudo systemctl start gunicorn
        echo "✅ Services restarted"
    else
        echo "❌ Management commands still broken"
    fi
else
    echo "❌ Django still broken - need full rebuild"
fi
///////////////////////////////
# 1. Fix packages (run script above)
# 2. Once Django is working, run migrations:
python manage.py makemigrations
python manage.py migrate --fake-initial
python manage.py migrate

# 3. Test your hotel management features:
python manage.py shell << 'EOF'
from apps.users.models import CustomUser
from apps.menu.models import MenuItem
print("✅ All models working")
EOF

# 4. Restart services:
sudo systemctl restart gunicorn nginx
//////////////////
(venv) ubuntu@hotel-management-server:~/hotel-management-backend$ pip install certifi==2023.7.22
pip install pytz==2023.3
pip install tzdata==2023.3
Collecting certifi==2023.7.22
  Downloading certifi-2023.7.22-py3-none-any.whl.metadata (2.2 kB)
Downloading certifi-2023.7.22-py3-none-any.whl (158 kB)
Installing collected packages: certifi
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
oci 2.150.3 requires pytz>=2016.10, which is not installed.
Successfully installed certifi-2023.7.22
Collecting pytz==2023.3
  Downloading pytz-2023.3-py2.py3-none-any.whl.metadata (22 kB)
Downloading pytz-2023.3-py2.py3-none-any.whl (502 kB)
Installing collected packages: pytz
Successfully installed pytz-2023.3
Collecting tzdata==2023.3
  Downloading tzdata-2023.3-py2.py3-none-any.whl.metadata (1.4 kB)
Downloading tzdata-2023.3-py2.py3-none-any.whl (341 kB)
Installing collected packages: tzdata
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
kombu 5.5.0 requires tzdata==2025.1; python_version >= "3.9", but you have tzdata 2023.3 which is incompatible.
Successfully installed tzdata-2023.3
(venv) ubuntu@hotel-management-server:~/hotel-management-backend$


