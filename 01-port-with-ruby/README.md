To run this example:

1. First make sure that 'rails-example-project' exists within this folder and inside it, in the file 'Gemfile.lock' -> `nokogiri (1.13.1-x86_64-darwin)` is changed to `nokogiri (1.14.2-arm64-darwin)` if you are ona M1 M2 chip mac.
2. `docker build . -t rails-project`
3. `docker run -p 3000:3000 rails-project`