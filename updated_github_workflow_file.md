name: RaceDay Full Solution CI

on:
  push:
  pull_request:

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code Repository
        uses: actions/checkout@v4

      - name: Setup .NET 8 SDK Environment
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: 8.0.x

      - name: Restore Solution Packages
        run: dotnet restore RaceDay.sln

      - name: Build Solution Projects
        run: dotnet build RaceDay.sln --configuration Release --no-restore

      - name: Execute xUnit Tests
        run: dotnet test RaceDay.sln --configuration Release --no-build --verbosity normal