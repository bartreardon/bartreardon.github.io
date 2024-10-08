## Jamf smart group with lots of serial numbers and a bit of regex

Had a one off requirement recently to add around 100 unknown-to-jamf (not yet enrolled, not in DEP) devices to a smart group. I wanted the devices to be in the group as soon as possible so I didn't want to rely on an EA, so since I knew all the serial numbers I thought I would add these in explicitly to create the group.

The Jamf UI doesn't allow for things like "if item in list", at least not for serial numbers (that I could find). Faced with the prospect of adding in 100+ individual "where serial number is C0ABCDE12345" I needed something to scratch my "do as little work as possible and get the same result" itch.

Enter regex

The following regex will match a given serial number against a list (in this case, 3)

```
^.*(C0ABCDE12345|C0ABCDE67890|C0ABCDE99999).*$
```

To populate 100+ serial numbers though is a challenge. The regex field has a 255 character limit, although the UI won't tell you that if you paste in a fat chunk of text. Turns out with the standard 12 character Apple serial number you can fit 19 serials including regex into 254 characters.

So, it was just a matter of putting all my serials into a text editor, seperate into 19 per line, add regex and paste the result in.

The result is only 6 OR'd items in the smart group for "Serial Number matches regex" rather than 100+ individual "OR serial number equals OR Serial Number equals etc..."

And as a bonus - it actually worked ;)
