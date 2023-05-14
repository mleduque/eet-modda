cd ~/Games/installs/baldur && \
rm -Rf test/* && cd test && \
iedup ~/Games/gog/baldurs-gate-ii-enhanced-edition/ . && \
cp -R ~/Games/ssllibs/* . && \
cd game && \
modda install -m /home/mick/Games/dev/eet-modda/test_root/all_around_modda_test.yml --from-index 2 --to-index 3
