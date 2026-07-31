# Blocking IP Addresses

You may restrict access to a CloudscribeNG site using the **Blocked IP Addresses** feature within the site Administration area at /siteadmin/blockedipaddresses

[Permitted IPs](/permitting-ip-addresses) take precedence over blocked IPs, so it's possible to block a range of IPs then permit one IP within that range.

## How to block a Single IP Address

	
- Click **‘Add New IP Address’**
	
- In the modal window, select either **Single IP Address** or **IP Address Range** (as specified below), according to the IP Address you are entering
	
- Click '**Next**'
	
- Enter a valid **IPv4 or IPv6** address or address range (as specified below) in the **Blocked IP** field
	
- Optionally, add a **reason** for blocking the IP address or range (helpful for future reference)
	
- Click **‘Save’**

NB: Be careful not to block your own IP address.

## Bulk uploading IPs to block

You can also bulk upload blocked IPs using a CSV file.

### CSV format requirements:

	
- No header row
	
- Each entry should be: **IP_ADDRESS,reason** (or **IP_ADDRESS** if no reason)
	
- Each **IP_ADDRESS,reason** (or **IP_ADDRESS,** if no reason) should be on its own line
	
- Only valid IP v4 and v6 address formats or ranges (as specified below) permitted

### Steps:

	
- Click **‘Bulk Upload IP Addresses’**
	
- Select your CSV file
	
- Click **‘OK’**

The uploader will:

	
- Add all valid IPs, and reasons where provided
	
- Show up to 10 error messages for invalid entries
	
- Write to the System Log for each invalid entry

You can search blocked IPs using the search box, which checks both IPs and reasons.

## IP Address Range Formats

The following range formats are accepted:

	
- 192.168.0.0/255.255.255.0
	
- 192.168.0.10 - 192.168.10.20
	
- 192.168.10.10-20
	
- fe80::/10
	
- 192.168.0.0/16
