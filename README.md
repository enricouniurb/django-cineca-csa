CINECA CSA Interface
--------------------

App per Django Framework che consente di interfacciarsi al database di CINECA CSA

#### Oracle dblink setup
Scarica il client Oracle e installalo come utente root:
````
unzip SETUP/ORACLE_Setup/instantclient-basic-linux.x64-12.2.0.1.0.zip -d /opt/oracle
echo "/opt/oracle/instantclient_12_2" > /etc/ld.so.conf.d/oracle-instantclient1.conf
ldconfig

````
Per personalizzare le colonne delle tabelle/viste provenienti da CINECA CSA
bisogna ereditare l'app o modificare il contenuto in models.py.

I dati da CSA possono essere usati in due modi, lettura diretta (con cache)
oppure copia dati su tabelle locali.
La configurazione deve essere integrata nel settings.py del progetto.
Esempio di configurazione:

````
# CSA settings
CSA_V_ANAGRAFICA = 'V_ANAGRAFICA'
CSA_V_CARRIERA = 'V_CARRIERA'
CSA_V_CARRIERA_DOCENTI = 'V_CARRIERA_DOCENTI'
CSA_V_INCARICO_DIP = 'V_INCARICO_DIP'
CSA_V_RUOLO = 'V_RUOLO'

CSA_SQL_QUERY = 'SELECT * FROM {} where matricola={} ORDER BY {} DESC'

DATABASE_CSA = {
                # Add 'postgresql_psycopg2', 'mysql', 'sqlite3' or 'oracle'.
                'ENGINE': 'django.db.backends.oracle',
                'NAME': 'xe',
                'USER': 'USERNAME',
                'PASSWORD': 'PASSWD',
                'HOST': 'SERVER_HOST',
                'PORT': '1521',
                }

if 'csa' in INSTALLED_APPS:
    # if replica a scheduled sqlscript must replicate datas in default DB
    CSA_MODE = 'replica' # or 'native'

    if CSA_MODE == 'native':
        DATABASES['csa'] = DATABASE_CSA
        DATABASE_ROUTERS = ['csa.routers.ReadOnlyDbRouter',]
    elif CSA_MODE == 'replica':
        CSA_REPL_SCRIPT = 'csa.sqlalchemy_repl'
    else:
        raise Exception('CSA_MODE non configured in settings.py')
````

#### Nota su CINECA CSA

Se le colonne del tuo DB dovessero chiamarsi diversamente rispetto a CSA, puoi modificare il
mapping in models.py, modifica solo i valori e non le chiavi per garantire il funzionamento del codice:

````
CARRIERA_FIELDS_MAP = {'descr_aff_org': 'ds_aff_org',
                       'descr_sede':    'ds_sede',
                       'descr_inquadramento': 'ds_inquadr',
                       'descr_profilo':       'ds_profilo',
                       'attivita':      'attivita',
                       'data_inizio_rapporto': 'dt_rap_ini',
                       'data_inizio':   'dt_ini',
                       'data_fine':     'dt_fin',
                       'inquadramento': 'inquadr',
                       'ruolo': 'ruolo'}

# per i docenti invence rimuoviamo gli attributi inutili e aggiungiamo quelli specifici
CARRIERA_DOCENTE_FIELDS_MAP = CARRIERA_FIELDS_MAP.copy()
del CARRIERA_DOCENTE_FIELDS_MAP['descr_profilo']
CARRIERA_DOCENTE_FIELDS_MAP.update({"aff_org": "aff_org",
                                    "ds_ruolo": "ds_ruolo",
                                    "ds_attivita": "ds_attivita",
                                    "dt_avanz" : "dt_avanz",
                                    "dt_prox_avanz" : "dt_prox_avanz",
                                    "cd_sett_concors" : "cd_sett_concors",
                                    "ds_sett_concors" : "ds_sett_concors",
                                    "cd_ssd" : "cd_ssd",
                                    "ds_ssd" : "ds_ssd",
                                    "area_ssd" : "area_ssd",
                                    "ds_area_ssd" : "ds_area_ssd",
                                    "scatti" : "scatti",
                                    "inquadramento": "inquadr",
                                    "descr_inquadramento": "ds_inquadr"})

INCARICHI_FIELDS_MAP = {'data_doc': 'data_doc',
                        'num_doc': 'num_doc',
                        'tipo_doc': 'tipo_doc',
                        'descr_tipo': 'des_tipo',
                        'data_inizio':   'dt_ini',
                        'data_fine':     'dt_fin',
                        #'relaz_accomp': 'relaz_accomp',
                        'ruolo': 'ruolo'}
````
