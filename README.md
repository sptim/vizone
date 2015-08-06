# vizone

Simple helper script to edit bind9 zone files

## Description

In short terms:

The script freezes the zone, opens the zonefile in an editor, ensures that the serial number has been increased, and thaws the zone.

In detail:

1. For a given zonefile specified as argument the name of the containing directory is checked to be anything but "slave", otherwise the
   a message gets printed and the zone gets skipped. If the directory name is ether "internal" or "external" it is assumed to be the view
	 name for that zonefile.
2. The actual zone name gets extracted and the bind daemon gets instructed to freeze the zone. This suspends updated to dynamic zones and
   causes pending changes to be written to the zonefile. For static zones this step just fails.
3. The zonefile gets opened in the editor specified by the EDITOR environment variable or vi if not set. If the --readonly option is used
   the file gets opened in the pager specified by the PAGER environment variable instead (or view if unset).
4. When the editor quits and the modification time of the file has been altered, the serial number of the SOA record is checked. If it has
   not been increased the user gets prompted to either continue editing, let this script increase the serial number, or ignore the problem.
	 Be aware that the automatic increase option rewrites the zonefile and any custom formatting or comments get lost.
5. The zone gets thawed which causes bind to reload it from disk and reenables updated in dynamic zones.
6. If more zonefiles are in the argument list the script repeats the above steps for each one.


## Dependencies

The script is written in perl and uses the modules DNS::ZoneParse, Getopt::Long, File::Basename, and Cwd. Certainly you need an editor/pager and
bind9 including the rndc command.

If you use debian, install the following packages in addition to your favorite editor/pager (e.g. vim): perl, perl-base, libdns-zoneparse-perl,
bind9, bind9utils

## ToDo

- Add END block to thaw frozen zones in case of an abnormal exit.
- Build debian package for easier deployment/updating.
- Options to select the editor, selecting view, etc.
