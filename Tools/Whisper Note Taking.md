ffmpeg -i "C:\Users\John\Videos\..." -vn -acodec mp3 "C:\Users\John\Videos\....mp3"

whisper "C:\Users\John\Videos\xxe.mp3" --model small --language en --device cuda --output_format txt

