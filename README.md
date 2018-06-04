Commands:
dist forlder for Output after building:
D:\red-river-frontend\build\app\jspm\jspm_packages\npm\mobile-robotics-frontend@0.1.0\dist

Commands:
literServ  at D:\red-river-frontend.. bla bla:
lite-server -c litesrv.json
to install it: npm install lite-server -g

combined build command: 
tsc -p src/angular4/tsconfig.json && jspm build src/module.js dist/output.js --format cjs
--- new
tsc -p src/angular4/tsconfig.json && jspm build src/module.js dist/mobile-robotics.min.js --format cjs --minify

to copy  build files to redfront folder for preview:
robocopy D:\mobile-robotics-frontend\dist  D:\red-river-frontend\build\app\jspm\jspm_packages\npm\mobile-robotics-frontend@0.1.0\dist

/**************************************************************************/



link.....

https://bitbucket.org/kukaaustin/mobile-robotics-frontend/src/master/

https://css-tricks.com/fixing-tables-long-strings/

https://developer.mozilla.org/en-US/docs/Web/CSS/transform#matrix()

https://www.youtube.com/watch?v=PS96nkFfWcg
