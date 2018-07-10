# FIPS compliance

Enable FIPS compliance using the following registry key:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\fipsalgorithmpoli 

This can be disabled per application using the app config setting:

<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime> 
</configuration> 
