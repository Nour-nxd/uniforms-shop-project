# IMPORTING PACKAGES
import pandas as pd
import csv
import time
import re
import os


class bcolors:
    blue = '\033[94m'
    cyan = '\033[96m'
    red = '\033[91m'
    white = '\033[0m'
    bold = '\033[1m'
    purple = "\033[35m"
    header = '\033[95m'
    green = '\033[92m'
    yellow = '\033[93m'
    pink = '\033[95m'
    lightgrey = '\033[37m'


df = pd.read_csv("a-StarUniformData.csv")


def online_order():
    '''This is a docstring
    Write a description about the function here Anastasiia'''
    again = True
    global subtotal, total, del_method
    subtotal = 0
    total = 0
    os.remove("cust-cart.csv")  # function to empty the csv file (including column names)
    cust_file = open('cust-cart.csv', 'a', newline='')  # open the csv file to append it
    writer = csv.writer(cust_file)
    column = ["School", "Category", "Type", "Price", "Amount"]  # The name of the columns for the csv file
    writer.writerow(column)  # this will write the names of the columns which is the first line in the csv file
    # straight after the csv has been deleted so the other rows and data can be appended underneath it
    print(bcolors.purple + "*" * 20, "WELCOME", "*" * 20,
          "\nThis section is for online orders to shop for school items\n" + bcolors.white)
    while again:
        print(bcolors.cyan + "[" + bcolors.white, "1", bcolors.cyan + "]" + bcolors.white + "Primary School A")
        print(bcolors.cyan + "[" + bcolors.white, "2", bcolors.cyan + "]" + bcolors.white + "Primary School B")
        print(bcolors.cyan + "[" + bcolors.white, "3", bcolors.cyan + "]" + bcolors.white + "Primary School C")
        print(bcolors.cyan + "[" + bcolors.white, "4", bcolors.cyan + "]" + bcolors.white + "Primary School D")
        print(bcolors.cyan + "[" + bcolors.white, "5", bcolors.cyan + "]" + bcolors.white + "Secondary School A")
        print(bcolors.cyan + "[" + bcolors.white, "6", bcolors.cyan + "]" + bcolors.white + "Secondary School B")
        schools_list = ["Primary School A", "Primary School B", "Primary School C", "Primary School D",
                        "Secondary School A", "Secondary School B"]

        while True:
            try:
                s_index = int(input(bcolors.yellow + "Please choose a school from the list above\n>> " + bcolors.white))
                if 0 < s_index <= 6:
                    school_choice = schools_list[s_index - 1]
                    break
                else:
                    print(
                        bcolors.red + "Invalid input! \nPlease select a valid option from the menu above." + bcolors.red)

            except ValueError:
                print("Please select a valid number option from the menu above.")

        category_list = ["Girls", "Boys", "Both"]
        print(bcolors.cyan + "[" + bcolors.white, "1", bcolors.cyan + "]" + bcolors.white + "Girls")
        print(bcolors.cyan + "[" + bcolors.white, "2", bcolors.cyan + "]" + bcolors.white + "Boys")
        print(bcolors.cyan + "[" + bcolors.white, "3",
              bcolors.cyan + "]" + bcolors.white + "Accessories (Only available for primary schools)")
        while True:
            try:
                category = int(input(
                    bcolors.yellow + "Please choose what do you want to shop for from the list above\n>> " + bcolors.white))
                if 0 < category <= 3:
                    cat_choice = category_list[category - 1]
                    break
                else:
                    print(
                        bcolors.red + "Invalid input! \nPlease select a valid option from the menu above" + bcolors.white)
            except ValueError:
                print(bcolors.red + "Please select a valid *number* option from the menu above" + bcolors.white)

        # Here we extract the relevant information that the user has chosen like the school and the gender
        extract = df.loc[(df['School'] == school_choice) & (df['Category'] == cat_choice)]
        # Here we reset the index to start from 0
        extract.reset_index(drop=True, inplace=True)
        # Here we choose what columns we want to display to the user in the command line
        extract_col = extract[["Type", "Price"]]
        # the relevant part of the dataframe is displayed
        print(extract_col)
        # data validation when the user chooses the index of the row for the item
        while True:
            try:
                # the user is asked to enter the index of the row that corresponds to the item they want to buy
                item_index = int(input(
                    bcolors.yellow + "Select the index of the item you would like to buy e.g. 2\n>> " + bcolors.white))
                if 0 <= item_index <= max(extract_col.index):  # data validation
                    break
                else:
                    print(bcolors.red + "Please enter an index within the range" + bcolors.white)
            except ValueError:
                print(bcolors.red + "Please enter an index within the range" + bcolors.white)

        item_choice = extract_col.iloc[[item_index]]  # locate the row using the index the user has entered
        print(item_choice)
        i_price = item_choice["Price"]  # printing the row of the chosen item with the price
        float(i_price)
        print(bcolors.bold + "The price is £", float(i_price), bcolors.white)
        amount_item = input(
            bcolors.yellow + "Please enter the amount of items needed \n>> " + bcolors.white)  # the user is asked to enter the amount of items they want
        float(amount_item)
        subtotal = float(amount_item) * float(i_price)  # the subtotal is calculated
        print(bcolors.blue + "Subtotal is £", float(subtotal), bcolors.white)
        while True:
            print("\nWould you like to: ")
            print(bcolors.cyan + "[" + bcolors.white, "1", bcolors.cyan + "]" + bcolors.white + "Buy another item")
            print(bcolors.cyan + "[" + bcolors.white, "2", bcolors.cyan + "]" + bcolors.white + "Go to checkout")
            buy_again = input(">> ")
            if buy_again == "1":
                again = True
                break
            elif buy_again == "2":
                print(bcolors.blue + "############# CHECKOUT #############" + bcolors.white)
                again = False
                # display main menu
                break
            else:  # data validation
                print(bcolors.red + "Please enter 1 or 2" + bcolors.white)
            # subtotal += subtotal
        total += subtotal
        total = round(total, 2)

        data = [[school_choice, cat_choice, item_choice["Type"].to_string(index=False),
                 item_choice["Price"].to_string(index=False),
                 amount_item]]  # the data about the items bought that will be stored in the csv

        cust_file = open('cust-cart.csv', 'a', newline='')  # open the csv file to append it
        writer = csv.writer(cust_file)
        # writer.writerow(column)  # this will list out the names of the columns which are always the first entries
        writer.writerows(data)  # this funtion is used to append multiple rows of data
        cust_file.close()
        admin_file = open('items-bought.csv', 'a', newline='')  # open the csv file to append it
        writer = csv.writer(admin_file)
        writer.writerows(data)  # this funtion is used to append multiple rows of data
        admin_file.close()
    cust_df = pd.read_csv("cust-cart.csv")

    print(bcolors.pink + "Total £", total, bcolors.white)

    method = ["Delivery", "Collect"]
    while True:
        print("Would you like to have the item: ")
        print(bcolors.cyan + "[" + bcolors.white, "1",
              bcolors.cyan + "]" + bcolors.white + "Delivered (extra cost included)")
        print(bcolors.cyan + "[" + bcolors.white, "2", bcolors.cyan + "]" + bcolors.white + "Collected from the shop")
        method_choice = input(">> ")
        if method_choice == "1":
            print(
                bcolors.bold + "This is the total of your order with the additional delivery cost (£5)" + bcolors.white)
            print(total, " + 5 = ", "£", total + 5)
            print(bcolors.pink + "Total £", total + 5, bcolors.white)
            print(bcolors.yellow + " \nHere is a list of your order" + bcolors.white)
            print(cust_df)
            del_method = method[int(method_choice) - 1]
            checkout()
            break
        elif method_choice == "2":
            print("This is the total without any additional charge: ", bcolors.pink + "Total £", total, bcolors.white)
            print(bcolors.blue + "##############ORDER Details##############" + bcolors.white)
            print(cust_df)
            print(bcolors.purple + "You can come to the shop to collect your items from Mon-Fri 9am-6pm")
            print("****Thank you for shopping at the Uniforms shop****" + bcolors.white)
            del_method = method[int(method_choice) - 1]
            checkout()
            break
        else:  # data validation
            print("Please enter 1 or 2")
        return del_method
    print("=" * 50)


################################################################
def admin():
    '''This is a docstring
    Write a description about the function here Amaara'''
    time.sleep(0.2)
    print(bcolors.purple + "*" * 20, "WELCOME", "*" * 20, "\nThis section is for admin only!\n" + bcolors.white)
    while True:
        username = input(bcolors.yellow + "Please enter the username\n>> " + bcolors.white)
        password = input(bcolors.yellow + "Please enter the password\n>> " + bcolors.white)
        if username == "admin" and password == "123":
            print(bcolors.green + "Valid details!" + bcolors.white)
            break
        else:
            print(bcolors.red + "Wrong username and/or password! Try again" + bcolors.white)
    while True:
        print("\nWould you like to: ")
        print(bcolors.cyan + "[" + bcolors.white, "1",
              bcolors.cyan + "]" + bcolors.white + "Display all the items bought")
        print(bcolors.cyan + "[" + bcolors.white, "2", bcolors.cyan + "]" + bcolors.white + "Display customer details")
        print(bcolors.cyan + "[" + bcolors.white, "3",
              bcolors.cyan + "]" + bcolors.white + "Display all the appointments")
        print(bcolors.cyan + "[" + bcolors.white, "4", bcolors.cyan + "]" + bcolors.white + "Go back to main menu")
        buy_again = input(">> ")
        if buy_again == "1":
            items_bought_df = pd.read_csv("items-bought.csv")
            print(items_bought_df)
            break
        elif buy_again == "2":
            cust_details = open("myfileDelivery.txt", "r")
            print(cust_details.read())
            break
        elif buy_again == "3":
            cust_app = open("appointments.txt", "r")
            print(cust_app.read())
            break
        elif buy_again == "4":
            print("main menu")
            break
        else:  # data validation
            print(bcolors.red + "Please enter 1-3" + bcolors.white)


################################################################
def checkout():
    '''This is a docstring
        Write a description about the function here Amaara'''
    time.sleep(0.2)
    print(bcolors.blue + "##############CUSTOMER Details##############" + bcolors.white)
    while True:
        try:
            title = str(input("Please enter your title \n>>"))
            if title.isalpha():
                if 0 < len(title) < 5:
                    break
            else:
                print(bcolors.red + "Invalid input, please enter your title (e.g. miss, sir)" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your title (e.g. miss, sir)" + bcolors.white)

    while True:
        try:
            firstName = str(input("Please enter your first name \n>>"))
            if firstName.isalpha():
                if 0 < len(firstName) < 25:
                    break
            else:
                print(
                    bcolors.red + "Invalid input, please try again. Your name should not be more than 25 characters" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your name." + bcolors.white)

    while True:
        try:
            lastName = str(input("Please enter your last name \n>>"))
            if lastName.isalpha():
                if 0 < len(lastName) < 25:
                    break
            else:
                print(
                    bcolors.red + "Invalid input, please try again. Your name should not be more than 25 characters" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your name." + bcolors.white)
    while True:
        try:
            telnum = str(input("Please enter your phone number \n>>"))
            if len(str(telnum)) == 11:
                break
            else:
                print(bcolors.red + "Invalid input. Please enter a UK valid number" + bcolors.white)
        except ValueError:
            print(bcolors.red + "Invalid input. Please enter a UK valid number" + bcolors.white)

    regex = '^[a-z0-9]+[\._]?[a-z0-9]+[@]\w+[.]\w{2,3}$'
    while True:
        email = input("Please enter your e-mail \n>>")
        if (re.search(regex, email)):
            break
        else:
            print("Invalid Email")

    address = input("Please enter your address \n >> ")
    while True:
        try:
            postcode = str(input("Please enter your PostCode \n>>"))
            if len(str(postcode)) == 7 or len(str(postcode)) == 6:
                break
            else:
                print(bcolors.red + "Invalid Entry. Please enter a valid UK postcode" + bcolors.white)
        except ValueError:
            print(bcolors.red + "Invalid Entry. Please enter a valid UK postcode" + bcolors.white)

    while True:
        try:
            city = str(input("Please enter city \n>>"))
            if city.isalpha():
                if 0 < len(city) < 30:
                    break
            else:
                print(
                    bcolors.red + "Invalid input, please try agian. The name of the city should be less than 30 characters" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input. Please enter the name of the city you live in" + bcolors.white)

    time.sleep(0.5)
    print(bcolors.blue + "*****Thank you*****")
    print("Here is a receipt for your order")
    time.sleep(0.5)
    print("############### RECEIPT ##############" + bcolors.white)
    print(bcolors.bold + "Title: " + bcolors.white, title)
    print(bcolors.bold + "First Name: " + bcolors.white, firstName)
    print(bcolors.bold + "Last Name: " + bcolors.white, lastName)
    print(bcolors.bold + "Phone Number: " + bcolors.white, telnum)
    print(bcolors.bold + "Email: " + bcolors.white, email)
    print(bcolors.bold + "Address: " + bcolors.white, address)
    print(bcolors.bold + "Postcode: " + bcolors.white, postcode)
    print(bcolors.bold + "City: " + bcolors.white, city)
    print(bcolors.bold + "Total £: " + bcolors.white, total)
    print(bcolors.bold + "Delivery method: " + bcolors.white, del_method)

    file1 = open("myfileDelivery.txt", "a")
    file1.write("Title: {}".format(title) + "\n")
    file1.write("First Name: {}".format(firstName) + "\n")
    file1.write("Last Name: {}".format(lastName) + "\n")
    file1.write("Phone Number:{}".format(telnum) + "\n")
    file1.write("Email: {}".format(email) + "\n")
    file1.write("Address: {}".format(address) + "\n")
    file1.write("Postcode: {}".format(postcode) + "\n")
    file1.write("City: {}".format(city) + "\n")
    file1.write("City: {}".format(del_method) + "\n")
    file1.write("=" * 30)
    file1.close()


################################################
# BOOKING APPOINTMENTS
#################################################
def booking_appointment():
    '''This is a docstring
        Write a description about the function here Anastasiia'''
    time.sleep(0.2)
    print(bcolors.blue + "##############CUSTOMER Details##############" + bcolors.white)
    while True:
        try:
            title = str(input("Please enter your title \n>>"))
            if title.isalpha():
                if 0 < len(title) < 5:
                    break
            else:
                print(bcolors.red + "Invalid input, please enter your title (e.g. miss, sir)" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your title (e.g. miss, sir)" + bcolors.white)

    while True:
        try:
            firstName = str(input("Please enter your first name \n>>"))
            if firstName.isalpha():
                if 0 < len(firstName) < 25:
                    break
            else:
                print(
                    bcolors.red + "Invalid input, please try again. Your name should not be more than 25 characters" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your name." + bcolors.white)

    while True:
        try:
            lastName = str(input("Please enter your last name \n>>"))
            if lastName.isalpha():
                if 0 < len(lastName) < 25:
                    break
            else:
                print(
                    bcolors.red + "Invalid input, please try again. Your name should not be more than 25 characters" + bcolors.white)
        except ValueError:
            print(
                bcolors.red + "Invalid input, please enter your name." + bcolors.white)
    while True:
        try:
            telnum = str(input("Please enter your phone number \n>>"))
            if len(str(telnum)) == 11:
                break
            else:
                print(bcolors.red + "Invalid input. Please enter a UK valid number" + bcolors.white)
        except ValueError:
            print(bcolors.red + "Invalid input. Please enter a UK valid number" + bcolors.white)

    regex = '^[a-z0-9]+[\._]?[a-z0-9]+[@]\w+[.]\w{2,3}$'
    while True:
        email = input("Please enter your e-mail \n>>")
        if (re.search(regex, email)):
            break
        else:
            print("Invalid Email")

    while True:
        date = input('Please enter the date that is suitable for you (dd/mm/yyyy) \n >> ')
        try:
            valid_date = time.strptime(date, '%d/%m/%Y')
            break
        except ValueError:
            print('Invalid date!')

    while True:
        apTime = input('Please enter hour that is suitable for you (00:00 am/pm) \n>> ')
        try:
            valid_time = time.strptime(apTime, '%I:%M %p')
            break
        except ValueError:
            print('Invalid time!')
    bookingConf = input("Confirm your booking Y/N")
    if bookingConf == "Y" or bookingConf == "y":
        print("Here is a receipt for your measuring appointment")
        print("################Booking Details##############")
        print(bcolors.bold + "Title: " + bcolors.white, title)
        print(bcolors.bold + "First Name: " + bcolors.white, firstName)
        print(bcolors.bold + "Last Name: " + bcolors.white, lastName)
        print(bcolors.bold + "Phone Number: " + bcolors.white, telnum)
        print(bcolors.bold + "Email: " + bcolors.white, email)
        print(bcolors.bold + "Date of appointment: " + bcolors.white, date)
        print(bcolors.bold + "Time of appointment: " + bcolors.white, apTime)

        file2 = open("appointments.txt", "a")
        file2.write("Title: {}".format(title) + "\n")
        file2.write("First Name: {}".format(firstName) + "\n")
        file2.write("Last Name: {}".format(lastName) + "\n")
        file2.write("Phone Number:{}".format(telnum) + "\n")
        file2.write("Email: {}".format(email) + "\n")
        file2.write("Date of appointment: {}".format(date) + "\n")
        file2.write("Time of appointment: {}".format(apTime) + "\n")
        file2.write("=" * 30)
        file2.close()

    elif bookingConf == "N" or bookingConf == "n":
        main_menu()
    else:
        print(bcolors.red + "Please enter y/n" + bcolors.white)


#######################################
# MAIN MENU
#######################################
def main_menu():
    '''This is a docstring
        Write a description about the function here Amaara'''
    print(bcolors.bold + """
    $$\      $$\           $$\                                                     $$\                      $$$$$$\                 $$\   $$\ $$\   $$\ $$$$$$\ $$$$$$$$\  $$$$$$\  $$$$$$$\  $$\      $$\  $$$$$$\        
    $$ | $\  $$ |          $$ |                                                    $$ |                    $$  __$$\  $$\$$\        $$ |  $$ |$$$\  $$ |\_$$  _|$$  _____|$$  __$$\ $$  __$$\ $$$\    $$$ |$$  __$$\       
    $$ |$$$\ $$ | $$$$$$\  $$ | $$$$$$$\  $$$$$$\  $$$$$$\$$$$\   $$$$$$\        $$$$$$\    $$$$$$\        $$ /  $$ | \$$$  |       $$ |  $$ |$$$$\ $$ |  $$ |  $$ |      $$ /  $$ |$$ |  $$ |$$$$\  $$$$ |$$ /  \__|      
    $$ $$ $$\$$ |$$  __$$\ $$ |$$  _____|$$  __$$\ $$  _$$  _$$\ $$  __$$\       \_$$  _|  $$  __$$\       $$$$$$$$ |$$$$$$$\       $$ |  $$ |$$ $$\$$ |  $$ |  $$$$$\    $$ |  $$ |$$$$$$$  |$$\$$\$$ $$ |\$$$$$$\        
    $$$$  _$$$$ |$$$$$$$$ |$$ |$$ /      $$ /  $$ |$$ / $$ / $$ |$$$$$$$$ |        $$ |    $$ /  $$ |      $$  __$$ |\_$$$ __|      $$ |  $$ |$$ \$$$$ |  $$ |  $$  __|   $$ |  $$ |$$  __$$< $$ \$$$  $$ | \____$$\       
    $$$  / \$$$ |$$   ____|$$ |$$ |      $$ |  $$ |$$ | $$ | $$ |$$   ____|        $$ |$$\ $$ |  $$ |      $$ |  $$ | $$ $$\        $$ |  $$ |$$ |\$$$ |  $$ |  $$ |      $$ |  $$ |$$ |  $$ |$$ |\$  /$$ |$$\   $$ |      
    $$  /   \$$ |\$$$$$$$\ $$ |\$$$$$$$\ \$$$$$$  |$$ | $$ | $$ |\$$$$$$$\         \$$$$  |\$$$$$$  |      $$ |  $$ | \__\__|       \$$$$$$  |$$ | \$$ |$$$$$$\ $$ |       $$$$$$  |$$ |  $$ |$$ | \_/ $$ |\$$$$$$  |      
    \__/     \__| \_______|\__| \_______| \______/ \__| \__| \__| \_______|         \____/  \______/       \__|  \__|                \______/ \__|  \__|\______|\__|       \______/ \__|  \__|\__|     \__| \______/                                                                                                                                                                                                                                                                                                                                                                                                                                                  

    """ + bcolors.white)

    while True:
        print(bcolors.purple + "*" * 20, "Welcome to the online order", "*" * 20, bcolors.white)
        print(bcolors.cyan + "[" + bcolors.white, "1",
              bcolors.cyan + "]" + bcolors.white + " Shopping for all the schools")
        print(bcolors.cyan + "[" + bcolors.white, "2",
              bcolors.cyan + "]" + bcolors.white + " Book in for a measuring appointment")
        print(bcolors.cyan + "[" + bcolors.white, "3", bcolors.cyan + "]" + bcolors.white + " Admin")
        print(bcolors.cyan + "[" + bcolors.white, "4", bcolors.cyan + "]" + bcolors.white + " Exit")
        school = input(bcolors.yellow + "What schools do you want to shop for?\n>> " + bcolors.white)
        if school == "1":
            online_order()
        elif school == "2":
            booking_appointment()
        elif school == "3":
            admin()
        elif school == "4":
            print(bcolors.cyan + "THANK YOU FOR USING OUR PROGRAM" + bcolors.white)
            print(bcolors.red + "Exiting..." + bcolors.white)
            break
        else:
            print(bcolors.red + "Please select a number from the menu above (1-3)" + bcolors.white)


main_menu()