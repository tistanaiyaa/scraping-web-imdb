import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL IMDb Top Box Office
url = "https://www.imdb.com/chart/boxoffice/"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36"
}

# Mengambil halaman website
response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, 'html.parser')

# Mencari daftar film
movies = soup.find_all('li', class_='ipc-metadata-list-summary-item')[:10]  # Ambil 10 film pertama

movie_data = []
for movie in movies:
    try:
        # Judul film
        title = movie.find('h3').text.strip()

        # Rating film (jika tersedia)
        rating_tag = movie.find('span', class_='ipc-rating-star')
        rating = rating_tag.text.strip() if rating_tag else "N/A"

        # Gambar film
        image = movie.find('img')['src']

        movie_data.append([title, rating, image])
    
    except Exception as e:
        print(f"Error: {e}")
        continue

# Simpan ke dalam DataFrame & CSV
df = pd.DataFrame(movie_data, columns=['Judul Film', 'Rating', 'Gambar'])
df.to_csv("imdb_movies.csv", index=False)

print("Data berhasil disimpan di 'imdb_movies.csv'")
