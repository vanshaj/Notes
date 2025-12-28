1. Create *generic* secret from file
``kubectl create secret generic my-seceret --from-file=folder/file1 --from-file=folder/file2 -n custom``
2. Create *tls* secret from cert and key
``kubectl create secret tls cert-secret --cert=folder/file.crt --key=folder/file.key -n custom``
