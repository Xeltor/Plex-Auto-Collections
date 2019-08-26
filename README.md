# Plex Auto Collections
Python 3 script/[standalone builds](https://github.com/vladimir-tutin/Plex-Auto-Collections/tree/master/dist) that works off a configuration file to create/update Plex collection. Supports IMDB
lists as well as built in Plex filters such as actors, genres, year, studio and more. For more filters refer to the
[plexapi.video.Movie](https://python-plexapi.readthedocs.io/en/latest/modules/video.html#plexapi.video.Movie) 
documentation. Not everything has been tested, so results may vary based off the filter.

When parsing IMDB lists the script will create a list of movies that are missing from Plex. If an TMDb and Radarr api-key
are supplied then the option will be presented to pass the list of movies along to Radarr.

As well as updating collections based off configuration files there is the ability to add new collections based off 
filters, delete collections, search for collections and manage the collections in the configuration file.

Thanks to [/u/deva5610](https://www.reddit.com/user/deva5610) for [IMDBList2PlexCollection](https://github.com/deva5610/IMDBList2PlexCollection) which prompted 
the idea for a configuration based collection manager.

Subfilters also allows for a little more granular selection of movies to add to a collection. Unlike regular filters, a 
movie must match at least one value from each subfilter to be added to a collection.

# Configuration
Modify the supplied config.yml.template file.

If you do not want it to have the option to submit movies that are missing from IMDB lists do not include the api-key
for TMBd or radarr. A TMDb apikey is not required for regular operation.

If you want movies to add to Radarr but not automatically search, change search to "false".

In order to find your Plex token follow 
[this guide](https://support.plex.tv/articles/204059436-finding-an-authentication-token-x-plex-token/).

Main filters allowed are actors, imdb-list as well as many attributes that can found in the [plexapi.video.Movie 
documentation](https://python-plexapi.readthedocs.io/en/latest/modules/video.html#plexapi.video.Movie). In addition 
subfilters for audio language, subtitle language and video-resolution have been created. Take note that the values for 
each must match what Plex has including special characters in order to match.

    subfilters:
        video-resolution: 1080 (further examples: sd, 720, 4k)
        audio-language: Français
        subtitle-language: English

If you do not want to use subfilters simply remove the section.

**Once complete it should look like**

    collections:
        Collection Name Here:
            imdb-list: https://www.imdb.com/list/ls068177081/
            actors: Seth Rogen, Aaron Paul
            studio: Lionsgate
                subfilters:
                    audio-language: English
                    genres: Action, Crime, Comedy
        Documentaries:
            genres: Documentary
    plex:
        library: Movies
        token: ###################
        url: http://192.168.1.5:32400
    radarr:
        url: http://192.168.1.5:7878/radarr/
        token: ###########################
        quality_profile_id: 4
        search: true
    tmdb:
        apikey: ############################

# Usage
[Standalone binaries]() have been created for both Windows and Linux.

If you would like to run from Python I have only tested this fully on Python 3.7.4. Dependencies must be installed by running

    pip install -r requirements.txt
    
If there are issues installing PyYAML 1.5.4 try

    pip install -r requirements.txt --ignore-installed
    
Make sure that plexapi is installed from the github source in requirements.txt. The one provided by pip contains a bug 
that will cause certain movies to crash the script when processing IMDB lists. To ensure that you are running the of 
plexapi check utils.py contains the following around line 172:

    def toDatetime(value, format=None):
    """ Returns a datetime object from the specified value.

        Parameters:
            value (str): value to return as a datetime
            format (str): Format to pass strftime (optional; if value is a str).
    """
    if value and value is not None:
        if format:
            value = datetime.strptime(value, format)
        else:
            value = datetime.fromtimestamp(int(value))
    return value

To run the script in a terminal run

    python plex_auto_collections.py
    
If you would like to schedule the script to run on a schedule the script can be launched to automatically and only 
based off the collection and then quit by running. This applies to the standalones as well.

    python plex_auto_collections.py --update
