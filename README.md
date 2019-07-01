I'm trying to find a sci-fi book that I read a decade ago and only have vague memories of. It's probably a fruitless task, but oh well, it's been bugging me for the past five years.

Here's my [Stackoverflow Sci-Fi and Fantasy](https://scifi.stackexchange.com/questions/63272/trying-to-find-a-story-were-there-are-two-humans-on-an-alien-world-of-many-speci) where I asked the community for some help. I posted success on June 17th, 2017, but after reading the book I thought was the right one on a plane flight June 30th, 2019, I realized I was mistaken :(

Therefore, the quest continues.

## Read wikipedia plot summaries for 6000+ books on a Goodreads list

1. Pull all pages related to the sci-fi & fantasy list containing 6000+ books
  ```
  wget --recursive --convert-links 'https://www.goodreads.com/list/show/3.Best_Science_Fiction_Fantasy_Books?page=2' --accept-regex '\?page=[0-9]{1,3}' -l 10000
  ```
2. Move pulled pages to `website/`
3. Extract book info from the scraped goodreads lists
  ```fish
  for f in website/*
      pup -f $f '.tableList td[width=100%] json{}' | jq '.[].children | .[0].children[0].text + "\t" + .[3].children[0].children[0].children[0].text + "\t" + .[0].href' -r >> books.tsv
  end
  ```
4. Construct "I'm feeling lucky" duck-duck-go wikipedia-links.txt
  ```fish
  for f in website/*
      pup -f $f '.tableList td[width=100%] json{}' | jq '.[].children | "https://duckduckgo.com/?q=!ducky+wikpedia " + .[0].children[0].text + " " + .[3].children[0].children[0].children[0].text' -r >> wikipedia-links.txt
  end
  sed -i -e 's/ /\+/g' wikipedia-links.txt
  ```
5. Make clickable links of out txt
  ```fish
  for f in (cat wikipedia-links.txt)
      set g (echo $f | sed 's/.*ducky//' | sed 's/+/ /g')
      echo "<a href='$f'>$g</a></br>" >> wikipedia-links.html
  end
  ```
