import datetime
import csv
import requests
from bs4 import BeautifulSoup
from fake_useragent import UserAgent


def collect_data(city_code='2398'):
    current_time = datetime.datetime.now().strftime('%d_%m_%Y_%H_%M')  # узнаем настоящее время
    ua = UserAgent()
    
    headers = {
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
        'User-Agent': ua.random
    }
    
    cookies = {
        'mg_geo_id': f'{city_code}'  # узнаём код города с сайта
    }
    
    response = requests.get(url='https://magnit.ru/promo/', headers=headers, cookies=cookies)  # отправляем запрос
    
    with open(f'index.html', 'w') as file:
        file.write(response.text)
    
    with open('index.html') as file:
        src = file.read()
        
    soup = BeautifulSoup(response.text, 'lxml')
    
    city = soup.find('a', class_='header__contacts-link_city').text.strip()
    cards = soup.find_all('a', class_='card-sale_catalogue')
    
    data = []
    for card in cards:
        card_title = card.find('div', class_='card-sale__title').text.strip()
        
        try:
            card_discount = card.find('div', class_='card-sale__discount').text.strip()  # пробуем найти карточку со скидкой, иначе продолжаем
        except AttributeError:
            continue
        
        
        card_price_old_integer = card.find('div', class_='label__price_old').find('span', class_='label__price-integer').text.strip()  # старая цена в копейках
        card_price_old_decimal = card.find('div', class_='label__price_old').find('span', class_='label__price-decimal').text.strip()  # страрая цена в рублях
        card_old_price = f'{card_price_old_integer}.{card_price_old_decimal}'  # старая цена
        
        card_price_integer = card.find('div', class_='label__price_new').find('span', class_='label__price-integer').text.strip()  # новая цена в копейках
        card_price_decimal = card.find('div', class_='label__price_new').find('span', class_='label__price-decimal').text.strip()  # новая цена в рублях
        card_price = f'{card_price_integer}.{card_price_decimal}'  # новая цена
        
        card_sale_date = card.find('div', class_='card-sale__date').text.strip().replace('\n', ' ')  # время действия скидки
        
        data.append(
            [card_title, card_discount, card_old_price, card_price, card_sale_date]
        )

        
    
    # создаем сsv файл в виде таблццы с нашими данными #

    with open(f'{city}_{current_time}.csv', 'w') as file:
        writer = csv.writer(file)
        
        writer.writerow(
            [
                'Продукт',
                'Процент скидки',
                'Новая цена',
                'Старая цена',
                'Время акции',
            ]
        )
        writer.writerows(
            data
        )
            
    print(f'Файл {city}_{current_time}.csv успешно записан!')
        
    
def main():
    collect_data(city_code='2398')
    
    
if __name__ == '__main__':
    main()
