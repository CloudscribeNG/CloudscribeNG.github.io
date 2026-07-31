# Permitting IP Addresses

If your website contains sensitive content, you can control access by allowing only specific IP addresses or a range of IP addresses using the **Permitted IP Addresses** feature in the administration area at /siteadmin/permittedipaddresses.

## How to add a permitted IP Address

	
1. Click **‘Add New IP Address’**
	
2. In the modal window, select either **Single IP Address** or **IP Address Range** (as specified below), according to the IP Address you are entering
	
3. Click '**Next**'
	
4. Enter a valid **IPv4 or IPv6** address or address range (as specified below) in the **Permitted IP** field
	
5. Optionally, add a **reason** for permitting the IP address or range (as specified below) (helpful for future reference)
	
6. Click **‘Save’**

Once permitted IP addresses have been specified, only users on these IP addresses will be able to access the site, unless specific [blocked IP addresses](/blocking-ip-addresses) have also been defined. Be sure to include your own IP address to avoid locking yourself out.

## Bulk uploading IP Addresses

To save time, you can upload multiple IP addresses via a CSV file.

### CSV format requirements:

	
- No header row
	
- Each entry should be: **IP_ADDRESS,reason** (or **IP_ADDRESS** if no reason)
	
- Each **IP_ADDRESS,reason** (or **IP_ADDRESS,** if no reason) should be on its own line
	
- Only valid IP v4 and v6 address formats or ranges (as specified below) are permitted

### Steps:

	
1. Click **‘Bulk Upload IP Addresses’**.
	
2. Select your CSV file.
	
3. Click **‘OK’**.

The system will process the file and:

	
- Add all valid IP addresses
	
- Display up to 10 error messages for invalid entries
	
- Write to the System Log for each invalid entry

You can search through permitted IPs using the search box, which looks through both IP addresses and reasons.

## IP Address Range Formats

The following range formats are accepted:

	
- 192.168.0.0/255.255.255.0
	
- 192.168.0.10 - 192.168.10.20
	
- 192.168.10.10-20
	
- fe80::/10
	
- 192.168.0.0/16
