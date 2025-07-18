# Setup JBoss (WildFly) and deploy sample web application in Mac
# ✅ Step 1: Install Java 17 or above
brew install openjdk@11
# Add this to your ~/.zshrc or ~/.bash_profile:
export JAVA_HOME="/usr/local/opt/openjdk@11"
export PATH="$JAVA_HOME/bin:$PATH"
# Confirm
java -version
# ✅ Step 2: Download and Install WildFly 10+
# Go to: https://www.wildfly.org/downloads/
# Download WildFly 10.x or later (preferably 26+)
# Extract it:
tar -xvzf wildfly-<version>.Final.tar.gz
mv wildfly-<version>.Final ~/wildfly

# ✅ Step 3: Start WildFly in Standalone Mode
cd $JBOSS_HOME/bin
./standalone.sh

# ✅ Step 4: Add Admin User
./add-user.sh

# Select Management User
# Set username/password
# Access admin console:
http://localhost:9990

# ✅ Step 5: Deploy Sample Java Web App (WAR)
# Build
mvn archetype:generate -DgroupId=com.example -DartifactId=sample-app -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
cd sample-app
mvn package

# Deploy
cp target/sample-app.war $JBOSS_HOME/standalone/deployments/

# ✅ Step 7: Enable HTTPS & Elytron (Security Framework)
# Generate self-signed certificate:
keytool -genkeypair -alias wildfly -keyalg RSA -keystore wildfly.keystore -storepass changeit
mv wildfly.keystore ~/wildfly/standalone/configuration

# Configure Elytron:
# In CLI:
./jboss-cli.sh --connect
/subsystem=elytron/key-store=myKS:add(path=wildfly.keystore,relative-to=jboss.server.config.dir,credential-reference={clear-text=changeit},type=JKS)

# ✅ Step 8: Test and Secure
# Access http://localhost:8080/sample-app — should redirect to Okta
# After login, it redirects back with session
# Use server.log to debug any issue



