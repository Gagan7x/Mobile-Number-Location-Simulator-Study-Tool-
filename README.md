# Mobile Number Region Locator (Educational Use Only)

import phonenumbers
from phonenumbers import geocoder, carrier
from geopy.geocoders import Nominatim
import folium
from streamlit_folium import st_folium
import streamlit as st

# Streamlit UI
st.set_page_config(page_title="Phone Number Locator", layout="centered")
st.title("📱 Mobile Number Location Simulator (Study Tool)")

st.markdown("""
This tool only shows the general region of the number based on public metadata.
It does NOT track GPS or give real-time address.
""")

# Input phone number
number = st.text_input("Enter a phone number with country code (e.g. +918878989755):")

if number:
    try:
        parsed_number = phonenumbers.parse(number)
        region = geocoder.description_for_number(parsed_number, 'en')
        provider = carrier.name_for_number(parsed_number, 'en')

        st.success(f"📍 Region: {region}")
        st.info(f"📶 Service Provider: {provider}")

        # Simulate region on map
        geolocator = Nominatim(user_agent="number_locator_study")
        location = geolocator.geocode(region)

        if location:
            lat, lon = location.latitude, location.longitude
            st.map({"lat": [lat], "lon": [lon]})

            # Interactive Map
            m = folium.Map(location=[lat, lon], zoom_start=6)
            folium.Marker([lat, lon], popup=f"{region} ({provider})").add_to(m)
            st_folium(m, width=700, height=500)
        else:
            st.warning("Could not geocode the region.")
    except Exception as e:
        st.error(f"Error: {e}")
