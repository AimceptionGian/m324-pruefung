Die Pipeline ist so strukturiert, dass zuerst der Job "build" ausgeführt wird, welcher die Applikation mit Checkout für die Pipeline verfügbar macht, anschliessend eine stabile Node Version lädt, die Node Modules installiert, die Applikation buildet und dann noch ein Artefakt davon hochlädt.
Der Job "test", soll auf der gleichen Version laufen, lädt dieses Artefakt dann wieder runter und führt die Tests aus. Dies funktioniert wahrscheinlich nicht, da ich den command falsch aufrufe bzw. die Scripts von dem Artefakt nicht richtig runtergeladen werden.

Das Deployment wäre ganz einfach umsetzbar, man müsste nur die variablen/secrets im github repo hinzufügen:
deploy:
runs-on: ubuntu-22.04

    needs: [build, test, lint]
    steps:
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ vars.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          tags: ${{ vars.DOCKERHUB_USERNAME }}/m324_pruefung:latest
