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
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Installing backend dependencies ... done
  Preparing metadata (pyproject.toml) ... error
  error: subprocess-exited-with-error

  × Preparing metadata (pyproject.toml) did not run successfully.
  │ exit code: 1
  ╰─> [50 lines of output]
      + meson setup /tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c /tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c/.mesonpy-aphi4eok -Dbuildtype=release -Db_ndebug=if-release -Db_vscrt=md -Dwheel=true -Dtests=false --native-file=/tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c/.mesonpy-aphi4eok/meson-python-native-file.ini
      The Meson build system
      Version: 1.9.0
      Source dir: /tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c
      Build dir: /tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c/.mesonpy-aphi4eok
      Build type: native build
      Project name: pycairo
      Project version: 1.28.0
      C compiler for the host machine: cc (gcc 11.4.0 "cc (Ubuntu 11.4.0-1ubuntu1~22.04.2) 11.4.0")
      C linker for the host machine: cc ld.bfd 2.38
      Host machine cpu family: aarch64
      Host machine cpu: aarch64
      Program python3 found: YES (/home/ubuntu/hotel-management-backend/venv/bin/python3)
      Compiler for C supports arguments -Wall: YES
      Compiler for C supports arguments -Warray-bounds: YES
      Compiler for C supports arguments -Wcast-align: YES
      Compiler for C supports arguments -Wconversion: YES
      Compiler for C supports arguments -Wextra: YES
      Compiler for C supports arguments -Wformat=2: YES
      Compiler for C supports arguments -Wformat-nonliteral: YES
      Compiler for C supports arguments -Wformat-security: YES
      Compiler for C supports arguments -Wimplicit-function-declaration: YES
      Compiler for C supports arguments -Winit-self: YES
      Compiler for C supports arguments -Winline: YES
      Compiler for C supports arguments -Wmissing-format-attribute: YES
      Compiler for C supports arguments -Wmissing-noreturn: YES
      Compiler for C supports arguments -Wnested-externs: YES
      Compiler for C supports arguments -Wold-style-definition: YES
      Compiler for C supports arguments -Wpacked: YES
      Compiler for C supports arguments -Wpointer-arith: YES
      Compiler for C supports arguments -Wreturn-type: YES
      Compiler for C supports arguments -Wshadow: YES
      Compiler for C supports arguments -Wsign-compare: YES
      Compiler for C supports arguments -Wstrict-aliasing: YES
      Compiler for C supports arguments -Wundef: YES
      Compiler for C supports arguments -Wunused-but-set-variable: YES
      Compiler for C supports arguments -Wswitch-default: YES
      Compiler for C supports arguments -Wno-missing-field-initializers: YES
      Compiler for C supports arguments -Wno-unused-parameter: YES
      Compiler for C supports arguments -fno-strict-aliasing: YES
      Compiler for C supports arguments -fvisibility=hidden: YES
      Did not find pkg-config by name 'pkg-config'
      Found pkg-config: NO
      Did not find CMake 'cmake'
      Found CMake: NO
      Run-time dependency cairo found: NO

      ../cairo/meson.build:31:12: ERROR: Dependency lookup for cairo with method 'pkgconfig' failed: Pkg-config for machine host machine not found. Giving up.

      A full log can be found at /tmp/pip-install-dao_fot8/pycairo_7e9a73b3df6b4415bd8df2aebcf26e0c/.mesonpy-aphi4eok/meson-logs/meson-log.txt
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
error: metadata-generation-failed

× Encountered error while generating package metadata.
╰─> See above for output.

note: This is an issue with the package mentioned above, not pip.
hint: See above for details.
(venv) ubuntu@hotel-management-server:~/hotel-management-backend$

