# AutoSongRecognizationSpotifyExtension


import speech_recognition as sr
import webbrowser
import spotipy
from spotipy.oauth2 import SpotifyOAuth

def listen_and_play_song():
    recognizer = sr.Recognizer()

    with sr.Microphone() as source:
        print("Listening...")

        recognizer.adjust_for_ambient_noise(source)

        audio = recognizer.listen(source)

    try:

        text = recognizer.recognize_google(audio)
        print("Transcription: ", text)

        search_url = f"https://www.google.com/search?q={text}"
        webbrowser.open(search_url)

        scope = "user-read-playback-state,user-modify-playback-state"
        sp = spotipy.Spotify(auth_manager=SpotifyOAuth(scope=scope))
        results = sp.search(q=text, limit=1, type="track")

        if results["tracks"]["items"]:
            song_uri = results["tracks"]["items"][0]["uri"]
            sp.start_playback(uris=[song_uri])
            print("Playing song on Spotify.")
        else:
            print("Song not found on Spotify.")
    except sr.UnknownValueError:
        print("Unable to recognize speech.")
    except sr.RequestError as e:
        print("Error: {0}".format(e))

listen_and_play_song()
