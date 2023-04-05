import requests

def search_movie():
    # Solicita ao usuário um título de filme para pesquisar
    title = input("Digite o título do filme que deseja pesquisar: ")

    # Faz a requisição à API do OMDb
    try:
        response = requests.get(f"http://www.omdbapi.com/?i=tt3896198&apikey=369282eb")
        response.raise_for_status()
    except requests.exceptions.HTTPError as errh:
        print("Erro HTTP:", errh)
        return
    except requests.exceptions.ConnectionError as errc:
        print("Erro de conexão:", errc)
        return
    except requests.exceptions.Timeout as errt:
        print("Tempo esgotado:", errt)
        return
    except requests.exceptions.RequestException as err:
        print("Erro na requisição:", err)
        return

    # Converte a resposta JSON em um dicionário Python
    data = response.json()

    # Verifica se a pesquisa retornou resultados
    if data["Response"] == "True":
        # Lista os títulos dos filmes encontrados
        for i, movie in enumerate(data["Search"]):
            print(f"{i+1}. {movie['Title']} ({movie['Year']}) - Pontuação IMDB: {movie['imdbRating']}")

        # Pergunta ao usuário se ele quer ver mais informações sobre um filme específico
        choice = input("Digite o número do filme que você deseja ver mais informações, ou pressione Enter para sair: ")
        if choice:
            try:
                choice = int(choice)
                if choice > 0 and choice <= len(data["Search"]):
                    imdb_id = data["Search"][choice-1]["imdbID"]
                    response = requests.get(f"http://www.omdbapi.com/?i=tt3896198&apikey=369282eb")
                    response.raise_for_status()
                    movie_data = response.json()
                    print(f"\nTítulo: {movie_data['Title']}")
                    print(f"Ano de Lançamento: {movie_data['Year']}")
                    print(f"Gênero: {movie_data['Genre']}")
                    print(f"Diretor: {movie_data['Director']}")
                    print(f"Atores: {movie_data['Actors']}")
                    print(f"Pontuação IMDB: {movie_data['imdbRating']}")
                    print(f"Enredo: {movie_data['Plot']}")
                else:
                    print("Escolha inválida.")
            except ValueError:
                print("Escolha inválida.")
        else:
            return
    else:
        print("Nenhum filme encontrado.")

if __name__ == "__main__":
    search_movie()
