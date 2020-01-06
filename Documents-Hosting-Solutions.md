
### Docs hosting solutions:

Few open source solutions for docs hosting(which we explored):
### 1. Readthedocs (RTD)
* Very popular, used in lots of open source projects. Almost de-fecto for open source documentation.
* Python Django based project.
* Supports
  - Automated building
  - Versioning
  - Docs hosting
  - Docs of Markdown(md) or reStructured(rst) format
  - Multiple languages
* Version Control Integration
  - Github
  - Gitlab
  - Bitbucket
  - SVN
- Require some effort in installation and setup with git repo
- Supports integration with whole repo or manually integrate a repo project as readthedocs project.
- Supports web hooks integration
  - A new projects in RTD server can be integrated and configured to use with existing GitHub/Gitlab project. After web hooks integration, any commits in the original repo will be pulled and updated in local copy.
    Logs will be build and updated in the hosted path.
- Automated building
- Versioning
  - When adding a project, clone the source-code in the RTD server filesystem
  - All the branches of source project will be available and shown in the RTD admin page
  - Multiple build versions can be maintained for a specific branch
  - At a time, only 1 of the builded versions can be make as public
- Typical scenario can be
  1. Create a docs repo in the Github/Gitlab, to host documentation
      - Better to separate out the documentation from the actual repo(s), so that doc can be maintained separately.
       This will be helpful,
        - with web hooks integration where any commit in the repo can be configured to build the latest docs.
        - Usually companies have content writers for public docs, which are separate then original code repo
  2. Create a project by adding Github/Gitlab repo in the RTD admin page(Either by integrating Github/Gitlab account or manually adding the project)
      - Github/Gitlab repo should be reachable from the RTD server
      - Project will be build when adding the project in RTD server
  3. Create a web hook in source project and configure in RTD project
  4. Share docs repo with content writers to populate the documents.

- Issues observed with RTD server
  1. Virtualenv,    
  Create multiple virtualenv w.r.t Python versions. Many times source projects doesn’t support the virtualenv version which might result in libraries incompatibility
  2. By default server runs in non-secure mode. SSL certificates need to add explicitly
  3. After building the project, Public docs will be accessible after following changes:
```
diff --git a/readthedocs/settings/dev.py b/readthedocs/settings/dev.py
index 6598136d2..53eb5408d 100644
--- a/readthedocs/settings/dev.py
+++ b/readthedocs/settings/dev.py
@@ -9,8 +9,8 @@ class CommunityDevSettings(CommunityBaseSettings):
 
     """Settings for local development"""
 
-    PRODUCTION_DOMAIN = 'localhost:8000'
-    WEBSOCKET_HOST = 'localhost:8088'
+    PRODUCTION_DOMAIN = '34.73.154.89:8000'
+    WEBSOCKET_HOST = '34.73.154.89:8088'
 
     @property
     def DATABASES(self):  # noqa
@@ -29,10 +29,10 @@ class CommunityDevSettings(CommunityBaseSettings):
 
     SLUMBER_USERNAME = 'test'
     SLUMBER_PASSWORD = 'test'  # noqa: ignore dodgy check
-    SLUMBER_API_HOST = 'http://127.0.0.1:8000'
-    PUBLIC_API_URL = 'http://127.0.0.1:8000'
+    SLUMBER_API_HOST = 'http://34.73.154.89:8000'
+    PUBLIC_API_URL = 'http://34.73.154.89:8000'
 
-    EXTERNAL_VERSION_URL = 'http://127.0.0.1:8000/static/external'
+    EXTERNAL_VERSION_URL = 'http://34.73.154.89:8000/static/external'
 
     BROKER_URL = 'redis://localhost:6379/0'
     CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
@@ -47,7 +47,7 @@ class CommunityDevSettings(CommunityBaseSettings):
     # 127.0.0.1 test
     # and navigate to http://test:8000
     CORS_ORIGIN_WHITELIST = (
-        'test:8000',
+        '34.73.154.89:8000',
     )
 
```


### 2. Docusauraus
- Node JS based hosting project
- Supports Docs of Markdown(md) or reStructured(rst) format
- Searching, 
  - Supports algolia documentation search
- Rich UI, as JS, React based
- Github pages support
- Doesn’t integrate with git repos directly
  - Supports 
      - Automated building
      - Docs hosting
      - Docs of Markdown(md) or reStructured(rst) format
      - Multiple languages
      - Github pages

### 3. PhpMyFaq
- Php based hosting project
- Very popular, used in lots of open source projects. Almost de-fecto for FAQs hosting.
- Host DB locally, no need for git repos support
- Very easy to install and setup public docs
- UI based docs modification, no need for committing(link in git projects) or maintaining specific docs format
- Comes with lots of features:
  - Rich set of Admin panel
  - Dashboaard, showing summary of visits, Articles, Comments, Open questions, Users
  - Role based access model
  - Content as FAQ based documentation
  - Multi-level FAQ categories
  - Supports
    - Open Questions
    - Comments
    - FAQ Glossary
    - FAQ News
    - FAQ attachments
  - Statistics
  - Backup data, logfiles
  - Rich set of configuration
- Docs can be added by anyone(configuration parameter)
- Docs can be made a public/non-public with 1 click
- Once installed, no need to interact with server. Almost everything can be done via UI.


