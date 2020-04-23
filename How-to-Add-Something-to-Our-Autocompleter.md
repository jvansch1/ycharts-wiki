Useful notes for django-autocompleter as they pertain to YCharts Development

## Setting up
In order to add a new item type to the autocompleter, you'll need to setup a new AutocompleterProvider. This is done by importing and extending one of two base AutoCompleterProvider classes. AutocompleterModelProvider is for autocompleting on models such as Companies or Indicators.  AutocompleterDictProvider is for anything else (in our case, metrics).
    
    # Registy and signal_registry will be discussd elsewhere.
    from autocompleter import registry, signal_registry, AutocompleterModelProvider

    class IndicatorAutocompleteProvider(AutocompleterModelProvider):
        model = Indicator
        provider_name = "ind"

        settings = {'MAX_EXACT_MATCH_WORDS': 0}

        def get_data(self):
            return {}

    registry.register("main", IndicatorAutocompleteProvider, {'MIN_LETTERS': 2, 'MAX_RESULTS': 6})
    signal_registry.register(Indicator)

## Aliasing
Django-autocompleter comes equipped with the ability to alias terms, both one-way and two-way.  This allows for users to get items that we store under one name by typing in another popular name for that item.  Generally, we want two-way aliases, but in certain circumstances, one-way is more correct.

Scenario:

* User types 'PE Ratio'
* We store 'Price to Earnings Ratio', but alias {'Price to Earnings': 'PE'}
* User gets 'Price to Earnings Ratio' in results.


When creating a phrase alias dict, the key should be what we store in the db.  That is, if we store "Home Price Index" but want users to be able to search for "House Price Index" and get "Home Price Index", the dict should look like this:

    {'Home': 'House'}

This is more pertinent for one-way aliases.  We have not followed this to this point, but it doesn't *really* matter in two-way aliasing.  Everything will get mapped to everything else.

### Two-way Aliasing

To get two-way phrase aliasing, override get_phrase_aliases() in the autocompleter provider.

    @classmethod
    def get_phrase_aliases(cls):
        phrase_aliases = {
            'Revenue': 'Turnover',
            'EV': 'Enterprise Value',
            'PE': ['price to earnings', 'price earnings']
        }
        return phrase_aliases

This will give you the following mapping:

    returned_phrase_aliases = {
        'enterprise value': ['ev'],
        'ev': ['enterprise value'],
        'revenue': ['turnover'],
        'turnover': ['revenue'],
        'pe': ['price to earnings', 'price earnings'],
        'price earnings': ['pe', 'price to earnings'],
        'price to earnings': ['pe', 'price earnings']
    }

In short, everything gets aliased to everything else.

### One-way Aliasing

To get one-way aliasing, ovverride get_one_way_phrase_aliases():

        @classmethod
        def get_one_way_phrase_aliases(cls):
        phrase_aliases = {
            'Revenue': 'Turnover',
            'EV': 'Enterprise Value',
            'PE': ['price to earnings', 'price earnings']
        }
        return phrase_aliases

This will give you:

    returned_phrase_aliases = {
        'ev': ['enterprise value'],
        'pe': ['price to earnings', 'price earnings'],
        'revenue': ['turnover']
    }

The relationship is not flipped, so it is a lot more rigid.  Typing "Turnover" will show results for "Revenue", but typing
"Revenue" will not show results for "Turnover."

### Keep in mind
Some things do not need to be aliased.  We do a bit of normalization to terms both before they are stored and when they are searched for.  One normalization that is particularly relevant is the handling of join characters and "&".  

* "&" will be replaced by "and".
* in ycharts.py, there is a setting called AUTOCOMPLETER_JOIN_CHARS.  These characters (at the time of this writing) will be replaced with both a space and an empty string.  'Hello-world' becomes ['Hello world', 'helloworld']

## Calculation Examples
```
# This structure dictates the set of calcs that will get autocompleted. 
#
# 'name' is what will actually be searched for
#
# Matches will be sorted in 'score' order, high to low. Any results that have the same score will be
# sorted lexographically by 'id'. 
#
# 'id' must be unique for each top level calc
#
# 'name' / 'display_name' should only be different if what you want to display is dfferent than what all 
# you want to search for.
#
# Notice that for two level calcs ... the format is not really part of the search result and scoring itself
# so much as it is part of the data returned as part of the result... The logic of a sub-calced being selected
# will all happen on the client side.

calc_info_for_search = [
{
    'name': 'PE Ratio',
    'id': 'pe_ratio',
    'score': 12,
    'data': {'search_name': 'pe_ratio', 'display_name': 'PE Ratio'}
}, 
{
    'name': 'Altman Z-Score',
    'id': 'altman_z_score',
    'score': 4,
    'data': {'search_name': 'altman_z_score', 'display_name': 'Altman Z-Score'}
}, 
{
    'name': 'EBIT Margin',
    'id': 'ebit_margin',
    'score': 3,
    'data': {'search_name': 'ebit_margin', 'display_name': 'EBIT Margin'}
}, 
{
    'name': 'EBITDA Margin',
    'id': 'ebitda_margin',
    'score': 3,
    'data': {'search_name': 'ebitda_margin', 'display_name': 'EBITDA Margin'}
}, 
{
    'name': 'KZ-Index',
    'id': 'kz_index',
    'score': 4,
    'data': {'search_name': 'kz_index', 'display_name': 'KZ Index'} 
}, 
{
    'name': 'Revenues',
    'id': 'revenues',
    'score': 7.3,
    'data': {'search_name': None, 'sub_calcs': 
        [
        {
            'name': 'TTM'
            'search_name': 'reveneus_ttm',
            'display_name': 'Revenues TTM'
        },
        {
            'name': 'Quarterly'
            'search_name': 'reveneus_quarterly',
            'display_name': 'Revenues Quarterly'
        },
        ]   
    }, 
]

# This structure is just a alias mapping ... 
# Notice that terms can map to multiple things... also, DONT WORRY ABOUT THE REQUISITE REVERSE MAPPINGS.
# That will happen for you automatically.
calc_aliases_for_search = {
    'Revenues' : ['Sales', 'Turnover'],
    'Dividend' : 'Divvies',
    'PE' : 'Price to Earnings',
    'EBIT' : 'Earnings Before Interest and Taxes',
    'EBITDA' : 'Earnings Before Interest, Taxes , Deprecriation and Amortization',
    'EV' : 'Enterprise Value',
}
```

