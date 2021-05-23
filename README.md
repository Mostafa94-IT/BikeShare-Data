# BikeShare-Data

import time
import pandas as pd
import numpy as np


CITY_DATA = { 'chicago': 'chicago.csv',
              'new york city': 'new_york_city.csv',
              'washington': 'washington.csv' }

def get_filters():
    person = input("Please enter your name:")

    """
    Asks user to specify a city, month, and day to analyze.
    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello {},\nI\'m Mostafa \nToday we will explore some bikeshare data together, lets get started!'.format(person))
    # get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    while True:
        city = input("Which city do you want to explore?\n      Please choose from the following 3 cities;\n      Chicago, New York city, or Washington: ")
        city = city.lower()
        if city in ['chicago', 'new york city', 'washington']:
            break
        else:
            print("invalid input. Please enter a valid input!")
    while True:
        month = input('Please choose a month from the first 6 months,\n      Type "all" to see data for the 6 months altogether: ')
        month = month.lower()
        if month in ['january', 'february', 'march', 'april', 'may', 'june', 'all']:
            break
        else:
            print("invalid input. Please enter a valid input!")
    # get user input for day of week (all, monday, tuesday, ... sunday)
    while True:
        day = input('If you want to see details related to a particular day,\n      Please provide the day or type "all" to get data for all the days: ')
        day = day.lower()
        if day in ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday', 'all']:
            break
        else:
            print("invalid input. Please enter a valid input!")
    print("Analyzing your data, please hold!")
    print('-'*70)
    return city, month, day
    
    
def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.
    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
     # load data file into a dataframe
    df = pd.read_csv(CITY_DATA[city])

    # convert the Start Time column to datetime
    df['Start Time'] = pd.to_datetime(df['Start Time'])

    # extract month and day of week from Start Time to create new columns
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.day_name()

    # filter by month if applicable
    if month != 'all':
        # use the index of the months list to get the corresponding int
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month = months.index(month) + 1

        # filter by month to create the new dataframe
        df = df[df['month'] == month]

    # filter by day of week if applicable
    if day != 'all':
        # filter by day of week to create the new dataframe
        df = df[df['day_of_week'] == day.title()]

    return df

def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # display the most common month
    most_common_month = df['month'].mode()[0]
    print("The most common month is: ", most_common_month)

    # display the most common day of week
    most_common_day = df['day_of_week'].mode()[0]
    print("The most common day of week  is: ", most_common_day)

    # display the most common start hour
    df['hour'] = df['Start Time'].dt.hour
    most_common_hour = df['hour'].mode()[0]
    print("The most common start hour is: ", most_common_hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*70)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # display most commonly used start station
    most_common_start_station = df['Start Station'].mode([0])
    print("The most commonly used start station is: ", most_common_start_station)

    # display most commonly used end station
    most_common_end_station = df['End Station'].mode()[0]
    print("The most commonly used end station is: ", most_common_end_station)

    # display most frequent combination of start station and end station trip
    most_frequent_combination = (df['Start Station'] + " " + df['End Station']).mode()[0]
    
    print("The most frequent combination of start station and end station trip is: ", most_frequent_combination)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*70)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # display total travel time
    print("Total travel time is: ", df['Trip Duration'].sum())

    # display mean travel time
    print("Average travel time is: ", df['Trip Duration'].mean())

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*70)


def user_stats(df, city):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # Display counts of user types
    user_types = df.groupby(['User Type'])['User Type'].count()
    print(user_types, "\n")
    if city != 'washington':
    # Display counts of gender
        gender = df.groupby(['Gender'])['Gender'].count()
        print(gender, "\n")
        # Display earliest, most recent, and most common year of birth
        earliest_birth_year = sorted(df.groupby(['Birth Year'])['Birth Year'])[0][0]
        most_recent_birth_year = sorted(df.groupby(['Birth Year'])['Birth Year'], reverse=True)[0][0]
        most_common_birth_year = df['Birth Year'].mode()[0]
        print("The earliest year of birth is ", earliest_birth_year)
        print("The most recent year of birth is ", most_recent_birth_year)
        print("The most common year of birth is ", most_common_birth_year)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*70)
def display_raw_data(df):

"""Asking the user if he wants to display raw data of 5 rows at a time"""
    rows = input('\nWould you like to see data for 5 rows?  Please type "yes" or "no" without the quotation marks\n ')
    if rows.lower() == 'yes':
        displayed_rows = 0
        while True:
            print(df.iloc[displayed_rows: displayed_rows+5])
            displayed_rows += 5
            extra_rows = input('Would you like to see extra 5 rows?  Please type "yes" or "no" without the quotation marks\n ')
            if extra_rows.lower() != 'yes':
                break
def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df, city)
        display_raw_data(df)

        restart = input('\nWould you like to restart?  Please type "yes" or "no" without the quotation marks\n ')
        if restart.lower() != 'yes':
            break
        print("restarting....")


if __name__ == "__main__":
	main()
